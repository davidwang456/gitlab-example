# NuGet Detector 调用流程指南

## 1. 环境准备

### 1.1 必要组件
- Black Duck Detect 工具
- Java 运行环境 (JRE 8 或更高版本)
- .NET SDK 或 Visual Studio Build Tools
- NuGet CLI 或 MSBuild (带 NuGet 集成)

### 1.2 环境变量设置
```bash
# 设置 Detect 工具路径
export DETECT_JAR_PATH="/path/to/detect-latest.jar"

# 设置 Black Duck 连接信息
export BD_URL="https://your-blackduck-instance.com"
export BD_API_TOKEN="your_api_token_here"

# 设置 .NET 工具路径
export MSBUILD_PATH="/c/Program Files/Microsoft Visual Studio/2022/BuildTools/MSBuild/Current/Bin/MSBuild.exe"
export NUGET_PATH="/path/to/nuget.exe"  # 如果使用独立 NuGet CLI
```

## 2. 扫描配置

### 2.1 基础配置
```bash
# 基础 Detect 命令
java -jar $DETECT_JAR_PATH \
  --detect.project.name="YourProject" \
  --detect.project.version.name="1.0.0" \
  --blackduck.url=$BD_URL \
  --blackduck.api.token=$BD_API_TOKEN \
  --detect.tools=DETECTOR \
  --detect.source.path="."
```

### 2.2 NuGet 特定配置
```bash
# 使用 MSBuild 的 NuGet 集成
java -jar $DETECT_JAR_PATH \
  --detect.msbuild.path="$MSBUILD_PATH" \
  --detect.nuget.solution.path="YourSolution.sln" \
  --detect.nuget.use.msbuild=true \
  --detect.dotnet.restore=false

# 使用独立 NuGet CLI
java -jar $DETECT_JAR_PATH \
  --detect.nuget.path="$NUGET_PATH" \
  --detect.nuget.solution.path="YourSolution.sln" \
  --detect.nuget.use.msbuild=false
```

## 3. 完整示例脚本

```bash
#!/bin/bash

# --- 配置变量 ---
PROJECT_NAME="YourDotNetProject"
PROJECT_VERSION="1.0.0"
SOLUTION_FILE="YourSolution.sln"
DETECT_JAR_PATH="./detect-latest.jar"
MSBUILD_PATH="/c/Program Files/Microsoft Visual Studio/2022/BuildTools/MSBuild/Current/Bin/MSBuild.exe"

# --- 检查环境 ---
echo "检查环境配置..."
if [ ! -f "$DETECT_JAR_PATH" ]; then
    echo "错误: Detect JAR 文件不存在: $DETECT_JAR_PATH"
    exit 1
fi

if [ ! -f "$MSBUILD_PATH" ]; then
    echo "错误: MSBuild 不存在: $MSBUILD_PATH"
    exit 1
fi

if [ ! -f "$SOLUTION_FILE" ]; then
    echo "错误: 解决方案文件不存在: $SOLUTION_FILE"
    exit 1
fi

# --- 构建 Detect 命令 ---
DETECT_CMD=(
    "java"
    "-jar" "$DETECT_JAR_PATH"
    "--detect.project.name=$PROJECT_NAME"
    "--detect.project.version.name=$PROJECT_VERSION"
    "--blackduck.url=$BD_URL"
    "--blackduck.api.token=$BD_API_TOKEN"
    "--blackduck.trust.cert=true"
    "--detect.tools=DETECTOR"
    "--detect.source.path=."
    "--detect.msbuild.path=\"$MSBUILD_PATH\""
    "--detect.nuget.solution.path=$SOLUTION_FILE"
    "--detect.nuget.use.msbuild=true"
    "--detect.dotnet.restore=false"
    "--logging.level.com.synopsys.integration=DEBUG"
)

# --- 执行扫描 ---
echo "开始执行 NuGet 依赖扫描..."
"${DETECT_CMD[@]}"

# --- 检查结果 ---
EXIT_CODE=$?
if [ $EXIT_CODE -ne 0 ]; then
    echo "扫描失败，退出码: $EXIT_CODE"
    exit $EXIT_CODE
else
    echo "扫描成功完成"
    exit 0
fi
```

## 4. 常见问题排查

### 4.1 网络问题
- 检查代理设置
- 验证 Black Duck 服务器可访问性
- 检查防火墙设置

### 4.2 权限问题
- 确保对项目目录有读写权限
- 检查 MSBuild 执行权限
- 验证 NuGet 包还原权限

### 4.3 配置问题
- 确认 MSBuild 版本兼容性
- 检查 NuGet 源配置
- 验证解决方案文件路径

## 5. 最佳实践

### 5.1 性能优化
- 使用 `--detect.nuget.excluded.directories` 排除不必要的目录
- 配置适当的 NuGet 缓存策略
- 使用 `--detect.parallel.processors` 启用并行处理

### 5.2 安全性
- 使用环境变量存储敏感信息
- 定期更新 API Token
- 限制扫描范围到必要目录

### 5.3 日志管理
- 启用详细日志用于调试
- 定期清理日志文件
- 使用 `--detect.logging.level` 控制日志级别 