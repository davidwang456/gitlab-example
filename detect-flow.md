# Detect 扫描 Python 项目流程

## 1. 检测器选择

Detect 支持多种 Python 项目检测器，按优先级顺序如下：

### 1.1 Pip Native Inspector
- 代码位置：`detectable/src/main/java/com/blackduck/integration/detectable/detectables/pip/inspector/PipInspectorDetectable.java`
- 触发条件：
  - 存在 setup.py 文件
  - 存在 requirements.txt 文件
  - 通过 `--detect.pip.requirements.path` 指定了 requirements 文件
- 精度：HIGH
- 依赖：
  - Python 可执行文件
  - Pip 可执行文件

### 1.2 Setuptools Pip
- 代码位置：`detectable/src/main/java/com/blackduck/integration/detectable/detectables/setuptools/tbuild/SetupToolsBuildDetectable.java`
- 触发条件：
  - 存在 pyproject.toml 文件
  - 文件中包含 `[build-system]` 和 `requires = ["setuptools"]`
- 精度：HIGH
- 依赖：
  - Pip 可执行文件

### 1.3 Pip Requirements Parse
- 代码位置：`detectable/src/main/java/com/blackduck/integration/detectable/detectables/pip/parser/RequirementsFileDetectable.java`
- 触发条件：
  - 存在 requirements.txt 文件
- 精度：LOW
- 依赖：无

## 2. 扫描流程

### 2.1 环境准备
1. 确保 Python 和 Pip 已安装
2. 设置环境变量或通过参数指定路径：
   ```bash
   --detect.python.path=/path/to/python
   --detect.pip.path=/path/to/pip
   ```

### 2.2 项目配置
1. 指定项目信息：
   ```bash
   --detect.project.name=your_project_name
   --detect.project.version.name=your_version
   ```

2. 指定 requirements 文件（可选）：
   ```bash
   --detect.pip.requirements.path=requirements.txt
   ```

### 2.3 执行扫描
1. 基本命令：
   ```bash
   java -jar detector.jar \
     --detect.source.path=. \
     --detect.tools=DETECTOR \
     --blackduck.url=https://your-blackduck-url \
     --blackduck.api.token=your-token
   ```

2. 精度控制（可选）：
   ```bash
   --detect.accuracy.required=NONE  # 允许低精度检测器
   ```

## 3. 检测器工作原理

### 3.1 Pip Native Inspector
1. 使用 pip-inspector.py 脚本分析依赖
2. 解析 setup.py 获取项目信息
3. 解析 requirements.txt 获取依赖信息
4. 生成依赖树

### 3.2 Setuptools Pip
1. 解析 pyproject.toml 文件
2. 使用 setuptools 获取项目信息
3. 分析项目依赖
4. 生成依赖树

### 3.3 Pip Requirements Parse
1. 直接解析 requirements.txt 文件
2. 提取依赖名称和版本
3. 生成简单的依赖列表

## 4. 常见问题解决

### 4.1 精度不匹配
- 问题：`Accuracy Not Met pip`
- 解决方案：
  - 添加 `--detect.accuracy.required=NONE`
  - 或确保使用高精度检测器（Pip Native Inspector 或 Setuptools Pip）

### 4.2 依赖解析失败
- 问题：`The Pip Inspector tree parse failed to produce output`
- 解决方案：
  - 确保 Python 和 Pip 版本兼容
  - 确保所有依赖已正确安装
  - 检查 requirements.txt 格式是否正确

### 4.3 项目名称无法识别
- 问题：无法自动识别项目名称
- 解决方案：
  - 添加 `--detect.pip.project.name=your_project_name`
  - 确保 setup.py 或 pyproject.toml 中包含正确的项目信息

## 5. 最佳实践

1. 使用虚拟环境：
   ```bash
   python -m venv venv
   source venv/bin/activate
   pip install -e .
   pip install -r requirements.txt
   ```

2. 确保项目结构完整：
   - setup.py 或 pyproject.toml
   - requirements.txt
   - 所有依赖包已安装

3. 使用最新版本的 Detect：
   - 支持最新的 Python 和 Pip 版本
   - 修复已知问题
   - 提供更好的兼容性

4. 添加详细日志：
   ```bash
   --logging.level.com.blackduck.integration=DEBUG
   ```

## 6. .NET 项目扫描流程

### 6.1 检测器选择

Detect 支持多种 .NET 项目检测器，按优先级顺序如下：

#### 6.1.1 版本兼容性
- Detect 8.x 版本支持：
  - .NET Core 2.1 - 6.0
  - .NET Framework 4.5.2 - 4.8
  - .NET Standard 2.0 - 2.1

- Detect 9.x 版本支持：
  - .NET Core 3.1 - 7.0
  - .NET Framework 4.6.1 - 4.8
  - .NET Standard 2.0 - 2.1

- 建议：
  - 使用最新版本的 Detect
  - 确保 .NET 版本在 Detect 支持范围内
  - 如果使用旧版本 .NET，可能需要使用旧版本 Detect

#### 6.1.2 NuGet Inspector
- 代码位置：`detectable/src/main/java/com/blackduck/integration/detectable/detectables/nuget/NugetInspectorDetectable.java`
- 触发条件：
  - 存在 .sln 文件
  - 存在 .csproj 文件
  - 存在 packages.config 文件
- 精度：HIGH
- 依赖：
  - .NET SDK
  - NuGet CLI

#### 6.1.3 Dotnet CLI
- 代码位置：`detectable/src/main/java/com/blackduck/integration/detectable/detectables/dotnet/DotnetDetectable.java`
- 触发条件：
  - 存在 .sln 文件
  - 存在 .csproj 文件
- 精度：HIGH
- 依赖：
  - .NET SDK

#### 6.1.3 NuGet Solution
- 代码位置：`detectable/src/main/java/com/blackduck/integration/detectable/detectables/nuget/solution/NugetSolutionDetectable.java`
- 触发条件：
  - 存在 .sln 文件
- 精度：MEDIUM
- 依赖：
  - .NET SDK

### 6.2 扫描流程

#### 6.2.1 环境准备
1. 确保 .NET SDK 已安装
   - 查看已安装的 .NET 版本：
     ```bash
     # 方法1：使用 dotnet 命令（推荐）
     dotnet --list-sdks
     
     # 方法2：使用 PowerShell
     Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | Get-ItemProperty -Name Version -ErrorAction SilentlyContinue | Where-Object { $_.PSChildName -Match '^(?!S)\p{L}'} | Select-Object PSChildName, Version
     
     # 方法3：查看安装目录
     # 打开 C:\Program Files\dotnet\sdk 目录
     
     # 方法4：通过控制面板
     # 打开控制面板 -> 程序和功能 -> 查找 .NET 相关项目
     
     # 方法5：使用 Visual Studio
     # 打开 Visual Studio -> 帮助 -> 关于 Microsoft Visual Studio
     ```

2. 设置环境变量或通过参数指定路径：
   ```bash
   --detect.dotnet.path=/path/to/dotnet
   --detect.nuget.path=/path/to/nuget
   ```

#### 6.2.2 项目配置
1. 指定项目信息：
   ```bash
   --detect.project.name=your_project_name
   --detect.project.version.name=your_version
   ```

2. 指定解决方案文件（可选）：
   ```bash
   --detect.nuget.solution.path=your_solution.sln
   ```

3. 指定 NuGet 配置文件（可选）：
   ```bash
   --detect.nuget.config.path=path/to/nuget.config
   ```
   
   NuGet.Config 文件示例：
   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <packageSources>
       <add key="nuget.org" value="https://api.nuget.org/v3/index.json" />
       <add key="company-repo" value="https://your-company-nuget-repo/api/v3/index.json" />
     </packageSources>
     <packageSourceCredentials>
       <company-repo>
         <add key="Username" value="your-username" />
         <add key="ClearTextPassword" value="your-password" />
       </company-repo>
     </packageSourceCredentials>
     <config>
       <add key="http_proxy" value="http://your-proxy:port" />
       <add key="no_proxy" value="localhost,127.0.0.1" />
     </config>
   </configuration>
   ```

#### 6.2.3 执行扫描
1. 基本命令：
   ```bash
   java -jar detector.jar \
     --detect.source.path=. \
     --detect.tools=DETECTOR \
     --blackduck.url=https://your-blackduck-url \
     --blackduck.api.token=your-token \
     --detect.dotnet.restore=true
   ```

2. 包管理器配置（可选）：
   ```bash
   --detect.nuget.packages.repo.url=https://your-nuget-repo
   --detect.nuget.packages.repo.username=your-username
   --detect.nuget.packages.repo.password=your-password
   ```

### 6.3 检测器工作原理

#### 6.3.1 NuGet Inspector
1. 解析 .sln 或 .csproj 文件
2. 使用 NuGet CLI 获取包信息
3. 分析依赖关系
4. 生成依赖树

#### 6.3.2 Dotnet CLI
1. 使用 `dotnet restore` 恢复依赖
2. 解析项目文件
3. 分析依赖关系
4. 生成依赖树

#### 6.3.3 NuGet Solution
1. 解析解决方案文件
2. 分析项目引用
3. 解析包配置
4. 生成依赖列表

### 6.4 常见问题解决

#### 6.4.1 依赖恢复失败
- 问题：`Failed to restore NuGet packages`
- 解决方案：
  - 确保 .NET SDK 版本兼容
  - 检查网络连接
  - 配置正确的包源

#### 6.4.2 项目解析失败
- 问题：`Failed to parse project file`
- 解决方案：
  - 确保项目文件格式正确
  - 检查 .NET SDK 版本
  - 确保所有项目文件可访问

#### 6.4.3 包源认证失败
- 问题：`Failed to authenticate with package source`
- 解决方案：
  - 检查认证信息
  - 配置正确的包源 URL
  - 确保有足够的权限

### 6.5 最佳实践

1. 使用正确的 .NET SDK 版本：
   ```bash
   --detect.dotnet.version=6.0
   ```

2. 配置包源：
   ```bash
   --detect.nuget.packages.repo.url=https://api.nuget.org/v3/index.json
   ```

3. 排除测试项目（可选）：
   ```bash
   --detect.nuget.excluded.modules=*.Tests
   ```

4. 添加详细日志：
   ```bash
   --logging.level.com.blackduck.integration=DEBUG
   ```

5. 使用包锁定文件（如果可用）：
   ```bash
   --detect.nuget.use.lockfile=true
   ```

6. 配置代理（如果需要）：
   ```bash
   --detect.nuget.proxy.host=your-proxy-host
   --detect.nuget.proxy.port=your-proxy-port
   --detect.nuget.proxy.username=your-proxy-username
   --detect.nuget.proxy.password=your-proxy-password
   ```

## 7. 使用 Visual Studio Build Tools 和 NuGet 扫描 .NET 项目

### 7.1 环境准备
1. 确保已安装：
   - Visual Studio Build Tools
   - NuGet MSBuild 扩展或独立 NuGet CLI

2. 确认工具路径：
   - MSBuild 路径：`C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\MSBuild.exe`
   - NuGet 路径（如果使用独立 CLI）：
     - 下载 NuGet.exe 并放在项目目录中
     - 或放在系统路径中
   - NuGet 配置文件路径：`C:\Users\YourUsername\AppData\Roaming\NuGet\NuGet.Config`

3. 如果使用 MSBuild 的 NuGet 功能：
   - 不需要指定 NuGet.exe 路径
   - 使用 `--detect.nuget.use.msbuild=true` 参数

### 7.2 扫描配置
1. 基本命令（使用 MSBuild 的 NuGet 功能）：
   ```bash
   java -jar detector.jar \
     --detect.source.path=. \
     --detect.tools=DETECTOR \
     --blackduck.url=https://your-blackduck-url \
     --blackduck.api.token=your-token \
     --detect.msbuild.path="C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\MSBuild.exe" \
     --detect.nuget.solution.path=your_solution.sln \
     --detect.dotnet.restore=false \
     --detect.nuget.use.msbuild=true
   ```

2. 如果使用独立 NuGet CLI：
   ```bash
   # 下载 NuGet CLI
   # 访问 https://www.nuget.org/downloads
   # 下载最新版本的 NuGet.exe
   # 将 NuGet.exe 放在项目目录中
   
   java -jar detector.jar \
     --detect.source.path=. \
     --detect.tools=DETECTOR \
     --blackduck.url=https://your-blackduck-url \
     --blackduck.api.token=your-token \
     --detect.nuget.path=./nuget.exe \
     --detect.nuget.solution.path=your_solution.sln \
     --detect.dotnet.restore=false
   ```

### 7.3 注意事项
1. 路径适配：
   - Visual Studio 版本号需要根据实际安装版本修改（如 2019、2022 等）
   - MSBuild 路径可能因安装选项不同而变化
   - NuGet 路径可能因安装方式不同而变化

2. 常见问题：
   - 如果找不到 MSBuild，检查 Visual Studio Build Tools 是否正确安装
   - 如果找不到 NuGet，检查 NuGet CLI 是否正确安装
   - 如果包恢复失败，检查 NuGet 配置文件和网络连接

3. 建议：
   - 使用完整路径而不是相对路径
   - 确保所有路径使用双引号包裹
   - 检查路径中的空格和特殊字符

### 7.4 最佳实践
1. 使用环境变量：
   ```bash
   # 设置环境变量
   set MSBUILD_PATH="C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\MSBuild.exe"
   set NUGET_PATH="C:\Program Files (x86)\NuGet\nuget.exe"
   
   # 在命令中使用环境变量
   --detect.msbuild.path=%MSBUILD_PATH%
   --detect.nuget.path=%NUGET_PATH%
   ```

2. 使用批处理脚本：
   ```batch
   @echo off
   set MSBUILD_PATH="C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\MSBuild\Current\Bin\MSBuild.exe"
   set NUGET_PATH="C:\Program Files (x86)\NuGet\nuget.exe"
   
   java -jar detector.jar ^
     --detect.source.path=. ^
     --detect.tools=DETECTOR ^
     --blackduck.url=https://your-blackduck-url ^
     --blackduck.api.token=your-token ^
     --detect.nuget.path=%NUGET_PATH% ^
     --detect.msbuild.path=%MSBUILD_PATH% ^
     --detect.nuget.solution.path=your_solution.sln ^
     --detect.dotnet.restore=false
   ```

3. 添加详细日志：
   ```bash
   --logging.level.com.blackduck.integration=DEBUG
   ``` 