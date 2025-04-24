# Black Duck Detect 输出处理指南

本文档提供了如何从 Black Duck Detect 工具的输出中提取 BOM URL，以及如何获取和使用 Bearer Token 来访问 Black Duck API 的详细步骤。

## 1. 从 Detect 输出提取 BOM URL

### 1.1 基本脚本示例

```bash
#!/bin/bash

# 运行 java -jar 命令并将输出保存到变量
OUTPUT=$(java -jar detect.jar --your-options-here)

# 从输出中提取 BOM URL
BOM_URL=$(echo "$OUTPUT" | grep "Black Duck Project BOM" | sed -E 's/.*\[MASKED\](\/api\/projects\/[^\/]+\/versions\/[^\/]+\/components).*/\1/')

# 检查是否成功提取 URL
if [ -z "$BOM_URL" ]; then
    echo "无法从输出中提取 BOM URL"
    exit 1
fi

echo "提取的 BOM URL: $BOM_URL"

# 提取 projectId 和 projectVersionId
PROJECT_ID=$(echo "$BOM_URL" | sed -E 's/\/api\/projects\/([^\/]+)\/versions.*/\1/')
PROJECT_VERSION_ID=$(echo "$BOM_URL" | sed -E 's/\/api\/projects\/[^\/]+\/versions\/([^\/]+).*/\1/')

echo "提取的 Project ID: $PROJECT_ID"
echo "提取的 Project Version ID: $PROJECT_VERSION_ID"
```

### 1.2 处理更复杂的输出

对于更复杂或不一致的输出格式，可以使用更健壮的方法：

```bash
#!/bin/bash

# 运行 java -jar 命令并将输出保存到文件
java -jar detect.jar --your-options-here > detect_output.txt

# 使用 awk 提取 BOM URL
BOM_URL=$(awk '/Black Duck Project BOM/ {
    for (i=1; i<=NF; i++) {
        if ($i ~ /\/api\/projects/) {
            gsub(/\[MASKED\]/, "", $i);
            gsub(/：/, "", $i);  # 处理可能的全角冒号
            print $i;
            exit;
        }
    }
}' detect_output.txt)

# 如果上面的方法失败，尝试使用 grep 和 sed 的组合
if [ -z "$BOM_URL" ]; then
    BOM_URL=$(grep -A 1 "Black Duck Project BOM" detect_output.txt | grep -o '/api/projects/[^[:space:]]*' | head -1)
fi

# 检查是否成功提取 URL
if [ -z "$BOM_URL" ]; then
    echo "无法从输出中提取 BOM URL，请检查输出文件: detect_output.txt"
    exit 1
fi

echo "提取的 BOM URL: $BOM_URL"
```

## 2. 获取 Bearer Token

### 2.1 认证 API 调用

```bash
# 设置 Black Duck URL 和 API TOKEN
BLACK_DUCK_URL="https://<Black Duck server URL>"
API_TOKEN="<API_TOKEN>"

# 获取 Bearer Token
echo "正在获取 Bearer Token..."
RESPONSE=$(curl -s -X POST \
  "${BLACK_DUCK_URL}/api/tokens/authenticate" \
  -H "Accept: application/vnd.blackducksoftware.user-4+json" \
  -H "Authorization: token ${API_TOKEN}")
```

### 2.2 处理认证响应

处理 Black Duck 返回的特殊格式响应：

```bash
# 处理异常格式的响应：{}{"bearerToken":"dd","expiresInMilliseconds:616"}
# 移除开头的 {}
FIXED_RESPONSE=$(echo "$RESPONSE" | sed 's/^{}//')

# 根据系统中可用的工具选择解析方法
if command -v jq &> /dev/null; then
    # 如果系统中有 jq，使用 jq 解析
    BEARER_TOKEN=$(echo "$FIXED_RESPONSE" | jq -r '.bearerToken')
else
    # 如果没有 jq，使用 grep 和 sed 解析
    BEARER_TOKEN=$(echo "$FIXED_RESPONSE" | grep -o '"bearerToken":"[^"]*"' | sed 's/"bearerToken":"//;s/"//')
fi

# 检查是否成功提取 token
if [ -z "$BEARER_TOKEN" ]; then
    echo "无法提取 Bearer Token，响应内容："
    echo "$RESPONSE"
    exit 1
fi

echo "成功获取 Bearer Token: ${BEARER_TOKEN}"
```

## 3. 使用 Bearer Token 调用 API

```bash
# 使用提取的 BOM URL 和 Bearer Token 调用 API
echo "正在获取项目组件列表..."
curl -s -X GET \
  "${BLACK_DUCK_URL}${BOM_URL}" \
  -H "Accept: application/json" \
  -H "Authorization: Bearer ${BEARER_TOKEN}" \
  | jq '.' 2>/dev/null || cat  # 如果有 jq 则美化输出，否则直接显示
```

## 4. 完整脚本示例

以下是一个将上述所有步骤结合的完整脚本示例：

```bash
#!/bin/bash

# ===== 配置参数 =====
DETECT_JAR_PATH="./detect.jar"
DETECT_ARGS="--your-detect-arguments-here"
BLACK_DUCK_URL="https://<Black Duck server URL>"
API_TOKEN="<API_TOKEN>"

# ===== 运行 Detect 扫描 =====
echo "开始运行 Black Duck Detect 扫描..."
OUTPUT=$(java -jar "$DETECT_JAR_PATH" $DETECT_ARGS)

# 将输出保存到文件以便调试
echo "$OUTPUT" > detect_output.txt

# ===== 提取 BOM URL =====
echo "从输出中提取 BOM URL..."
BOM_URL=$(echo "$OUTPUT" | grep "Black Duck Project BOM" | sed -E 's/.*\[MASKED\](\/api\/projects\/[^\/]+\/versions\/[^\/]+\/components).*/\1/')

# 如果上面的方法失败，尝试使用 awk
if [ -z "$BOM_URL" ]; then
    BOM_URL=$(awk '/Black Duck Project BOM/ {
        for (i=1; i<=NF; i++) {
            if ($i ~ /\/api\/projects/) {
                gsub(/\[MASKED\]/, "", $i);
                gsub(/：/, "", $i);
                print $i;
                exit;
            }
        }
    }' detect_output.txt)
fi

# 如果上面的方法还是失败，尝试使用 grep
if [ -z "$BOM_URL" ]; then
    BOM_URL=$(grep -A 1 "Black Duck Project BOM" detect_output.txt | grep -o '/api/projects/[^[:space:]]*' | head -1)
fi

# 检查是否成功提取 URL
if [ -z "$BOM_URL" ]; then
    echo "错误: 无法从输出中提取 BOM URL，请检查 detect_output.txt"
    exit 1
fi

echo "成功提取 BOM URL: $BOM_URL"

# ===== 提取项目 ID =====
PROJECT_ID=$(echo "$BOM_URL" | sed -E 's/\/api\/projects\/([^\/]+)\/versions.*/\1/')
PROJECT_VERSION_ID=$(echo "$BOM_URL" | sed -E 's/\/api\/projects\/[^\/]+\/versions\/([^\/]+).*/\1/')

echo "Project ID: $PROJECT_ID"
echo "Project Version ID: $PROJECT_VERSION_ID"

# ===== 获取 Bearer Token =====
echo "获取 Bearer Token..."
RESPONSE=$(curl -s -X POST \
  "${BLACK_DUCK_URL}/api/tokens/authenticate" \
  -H "Accept: application/vnd.blackducksoftware.user-4+json" \
  -H "Authorization: token ${API_TOKEN}")

# 处理响应
FIXED_RESPONSE=$(echo "$RESPONSE" | sed 's/^{}//')
if command -v jq &> /dev/null; then
    BEARER_TOKEN=$(echo "$FIXED_RESPONSE" | jq -r '.bearerToken')
else
    BEARER_TOKEN=$(echo "$FIXED_RESPONSE" | grep -o '"bearerToken":"[^"]*"' | sed 's/"bearerToken":"//;s/"//')
fi

# 检查是否成功提取 token
if [ -z "$BEARER_TOKEN" ]; then
    echo "错误: 无法提取 Bearer Token，响应内容："
    echo "$RESPONSE"
    exit 1
fi

echo "成功获取 Bearer Token: ${BEARER_TOKEN:0:10}..."

# ===== 调用 API 获取组件列表 =====
echo "获取项目组件列表..."
COMPONENTS_RESPONSE=$(curl -s -X GET \
  "${BLACK_DUCK_URL}${BOM_URL}" \
  -H "Accept: application/json" \
  -H "Authorization: Bearer ${BEARER_TOKEN}")

# 保存响应到文件
echo "$COMPONENTS_RESPONSE" > components_response.json

echo "组件列表已保存到 components_response.json"

# 如果有 jq，显示一些基本统计信息
if command -v jq &> /dev/null; then
    COMPONENTS_COUNT=$(echo "$COMPONENTS_RESPONSE" | jq '.totalCount // length')
    echo "组件总数: $COMPONENTS_COUNT"
fi

echo "处理完成!"
```

## 5. 注意事项和最佳实践

### 5.1 错误处理

- 添加足够的错误检查
- 保存中间输出以便调试
- 使用不同的提取方法作为备选

### 5.2 安全考虑

- 避免在脚本中硬编码 API 令牌
- 考虑使用环境变量或外部配置文件
- 限制 Bearer Token 的输出，避免完整显示

### 5.3 媒体类型

如果遇到 API 媒体类型的问题，可以尝试以下媒体类型：

```
application/json                                      # 基本 JSON
application/vnd.blackducksoftware.bill-of-materials-6+json  # BOM 格式 v6
application/vnd.blackducksoftware.component-detail-5+json   # 组件详情 v5
application/vnd.blackducksoftware.component-detail-4+json   # 组件详情 v4
```

### 5.4 处理大型响应

对于大型项目，组件列表可能非常大，建议：

- 使用分页参数 (`?limit=100&offset=0`)
- 处理响应时考虑内存使用
- 对大型 JSON 响应使用流式处理

### 5.5 调试技巧

遇到问题时：

- 检查 detect_output.txt 了解 Detect 的输出
- 使用 curl 的 `-v` 参数查看详细的 HTTP 请求和响应
- 检查 components_response.json 了解 API 响应内容 