# Black Duck Detect Shell 脚本解析

## 1. 脚本概述
`detect10.sh` 是一个用于下载和运行 Black Duck Detect 的 shell 脚本，主要功能包括：
- 自动下载最新版本的 Detect
- 管理 Detect 的版本
- 配置运行环境
- 执行 Detect 扫描

## 2. 主要功能模块

### 2.1 路径分隔符处理
```bash
get_path_separator() {
  # 检查系统是否为 Windows
  if [[ `uname` == *"NT"* ]] || [[ `uname` == *"UWIN"* ]]; then
    echo "\\"
  else
    echo "/"
  fi
}
```
- 根据操作系统类型返回正确的路径分隔符
- Windows 系统返回 `\`
- Unix/Linux 系统返回 `/`

### 2.2 版本管理
```bash
DETECT_RELEASE_VERSION=${DETECT_LATEST_RELEASE_VERSION}
DETECT_VERSION_KEY=${DETECT_VERSION_KEY:-DETECT_LATEST_10}
```
- 支持指定特定版本或使用最新版本
- 默认使用 DETECT_LATEST_10 作为版本键

### 2.3 组织名称和前缀处理
```bash
get_org_name() {
  # 根据版本号确定组织名称
  # 版本号 <= 9 使用 "synopsys"
  # 版本号 > 9 使用 "blackduck"
}

get_detect_name_prefix() {
  # 根据版本号确定 Detect 名称前缀
  # 版本号 <= 9 使用 "synopsys-detect"
  # 版本号 > 9 使用 "detect"
}
```

### 2.4 下载管理
```bash
get_detect() {
  # 下载 Detect JAR 文件
  # 支持本地缓存
  # 支持远程下载
}
```
- 检查本地是否有最新版本
- 如果没有则从远程下载
- 支持断点续传和缓存

### 2.5 Java 环境配置
```bash
set_detect_java_path() {
  # 设置 Java 路径
  # 优先级：DETECT_JAVA_PATH > JAVA_HOME > PATH
}
```
- 支持自定义 Java 路径
- 支持 Java 环境变量配置
- 支持系统默认 Java

### 2.6 运行 Detect
```bash
run_detect() {
  # 执行 Detect 扫描
  # 支持自定义参数
  # 支持日志记录
}
```
- 配置 Java 环境
- 执行 Detect 扫描
- 处理返回结果

## 3. 环境变量配置

脚本支持多种环境变量配置：

### 3.1 版本控制
- `DETECT_LATEST_RELEASE_VERSION`：指定版本
- `DETECT_VERSION_KEY`：版本键

### 3.2 下载配置
- `DETECT_SOURCE`：自定义下载源
- `DETECT_JAR_DOWNLOAD_DIR`：下载目录

### 3.3 Java 配置
- `DETECT_JAVA_PATH`：Java 路径
- `DETECT_JAVA_OPTS`：Java 选项

### 3.4 网络配置
- `DETECT_CURL_OPTS`：curl 选项
- `DETECT_DOWNLOAD_ONLY`：仅下载模式

## 4. 使用示例

### 4.1 基本使用
```bash
./detect10.sh --detect.project.name="项目名称" --detect.project.version.name="版本号"
```

### 4.2 指定版本
```bash
DETECT_LATEST_RELEASE_VERSION=10.2.0 ./detect10.sh
```

### 4.3 自定义 Java
```bash
DETECT_JAVA_PATH=/path/to/java ./detect10.sh
```

### 4.4 仅下载
```bash
DETECT_DOWNLOAD_ONLY=1 ./detect10.sh
```

## 5. 安全特性

### 5.1 敏感信息处理
```bash
# 对敏感参数进行脱敏处理
LOGGABLE_SCRIPT_ARGS=""
for i in $*; do
  if [[ $i == --blackduck.hub.password=* ]]; then
    LOGGABLE_SCRIPT_ARGS="$LOGGABLE_SCRIPT_ARGS --blackduck.hub.password=<redacted>"
  # ... 其他敏感参数处理
  fi
done
```

### 5.2 错误处理
- 下载失败处理
- 版本检查
- 环境检查

## 6. 最佳实践

### 6.1 版本管理
- 建议使用最新版本
- 必要时可以固定特定版本

### 6.2 环境配置
- 配置合适的 Java 环境
- 设置足够的内存限制

### 6.3 网络配置
- 配置代理（如需要）
- 设置超时时间

### 6.4 安全配置
- 保护敏感信息
- 使用安全的下载源

## 7. 注意事项

1. 确保系统已安装 Java 环境
2. 确保有足够的磁盘空间用于下载和缓存
3. 确保网络连接正常
4. 注意保护敏感信息
5. 定期更新到最新版本

这个脚本是 Black Duck Detect 的重要组成部分，它简化了 Detect 的安装和运行过程，提供了灵活的配置选项，并确保了安全性和可靠性。 