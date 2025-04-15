# GitLab Runner 17.5.0 Windows 安装指南

本文档提供了在 Windows 系统上安装 GitLab Runner 17.5.0 的详细说明。

## 安装方法

### 方法一：使用安装程序（推荐）

1. **下载安装程序**：
   - 访问 GitLab Runner 下载页面：https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe
   - 或者直接下载 17.5.0 版本：https://gitlab-runner-downloads.s3.amazonaws.com/v17.5.0/binaries/gitlab-runner-windows-amd64.exe

2. **安装步骤**：
   ```powershell
   # 1. 创建安装目录
   mkdir "C:\GitLab-Runner"
   
   # 2. 将下载的exe文件复制到安装目录并重命名
   copy gitlab-runner-windows-amd64.exe "C:\GitLab-Runner\gitlab-runner.exe"
   
   # 3. 以管理员身份运行PowerShell，执行以下命令
   cd "C:\GitLab-Runner"
   .\gitlab-runner.exe install
   .\gitlab-runner.exe start
   ```

### 方法二：使用 Scoop 包管理器

1. **安装 Scoop**（如果尚未安装）：
   ```powershell
   Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
   irm get.scoop.sh | iex
   ```

2. **安装 GitLab Runner**：
   ```powershell
   scoop install gitlab-runner
   ```

### 方法三：使用 Chocolatey 包管理器

1. **安装 Chocolatey**（如果尚未安装）：
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```

2. **安装 GitLab Runner**：
   ```powershell
   choco install gitlab-runner --version=17.5.0
   ```

## 安装 MSBuild 和 .NET Framework SDK

### 方法一：使用 Visual Studio Build Tools（推荐）

1. **下载 Visual Studio Build Tools**：
   - 访问 Visual Studio 下载页面：https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022
   - 下载 "Build Tools for Visual Studio 2022"

2. **安装步骤**：
   ```powershell
   # 1. 运行下载的安装程序
   # 2. 选择 "Visual Studio Build Tools"
   # 3. 在工作负载中选择：
   #    - .NET Framework 4.6.2 开发工具
   #    - .NET 桌面生成工具
   #    - MSBuild
   # 4. 在单个组件中确保选择：
   #    - Windows 10 SDK
   #    - .NET Framework 4.6.2 SDK
   ```

3. **验证安装**：
   ```powershell
   # 检查 MSBuild 版本
   & "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin\MSBuild.exe" /version

   # 检查 .NET Framework 版本
   Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | Get-ItemProperty -Name Version -ErrorAction SilentlyContinue | Where-Object { $_.PSChildName -Match '^(?!S)\p{L}'} | Select-Object PSChildName, Version
   ```

### 方法二：使用 Chocolatey 安装

1. **安装 Chocolatey**（如果尚未安装）：
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```

2. **安装 MSBuild 和 .NET Framework SDK**：
   ```powershell
   # 安装 MSBuild
   choco install microsoft-build-tools

   # 安装 .NET Framework SDK
   choco install dotnetfx
   ```

3. **验证安装**：
   ```powershell
   # 检查 MSBuild 是否在 PATH 中
   Get-Command msbuild

   # 检查 .NET Framework 版本
   Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | Get-ItemProperty -Name Version -ErrorAction SilentlyContinue | Where-Object { $_.PSChildName -Match '^(?!S)\p{L}'} | Select-Object PSChildName, Version
   ```

### 方法三：手动安装 .NET Framework SDK

1. **下载 .NET Framework SDK**：
   - 访问 Microsoft 下载中心：https://dotnet.microsoft.com/download/dotnet-framework
   - 下载 .NET Framework 4.6.2 Developer Pack

2. **安装步骤**：
   ```powershell
   # 运行下载的安装程序
   # 按照安装向导完成安装
   ```

3. **配置环境变量**：
   ```powershell
   # 添加 MSBuild 到 PATH
   $env:Path += ";C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin"
   
   # 永久设置环境变量
   [Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin", "Machine")
   ```

### 常见问题解决

1. **MSBuild 找不到**：
   ```powershell
   # 检查 MSBuild 路径
   Get-ChildItem -Path "C:\Program Files (x86)\Microsoft Visual Studio" -Recurse -Filter "MSBuild.exe"
   ```

2. **.NET Framework 版本问题**：
   ```powershell
   # 安装特定版本的 .NET Framework
   choco install dotnetfx --version=4.6.2
   ```

3. **权限问题**：
   ```powershell
   # 以管理员身份运行 PowerShell
   Start-Process powershell -Verb RunAs
   ```

### 验证安装完成

```powershell
# 检查 MSBuild
& "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin\MSBuild.exe" /version

# 检查 .NET Framework
Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | Get-ItemProperty -Name Version -ErrorAction SilentlyContinue | Where-Object { $_.PSChildName -Match '^(?!S)\p{L}'} | Select-Object PSChildName, Version

# 检查环境变量
$env:Path
```

## 注册 Runner

安装完成后，需要注册 Runner：

```powershell
# 以管理员身份运行PowerShell
cd "C:\GitLab-Runner"
.\gitlab-runner.exe register
```

在注册过程中，你需要提供：
1. GitLab 实例的 URL
2. 注册令牌（从 GitLab 项目的 Settings > CI/CD > Runners 获取）
3. Runner 的描述
4. Runner 的标签
5. 执行器类型（推荐使用 shell 或 docker）

## 配置 Runner 作为服务

```powershell
# 安装为服务
.\gitlab-runner.exe install

# 启动服务
.\gitlab-runner.exe start

# 检查状态
.\gitlab-runner.exe status
```

## 证书配置

如果遇到 SSL/TLS 证书验证问题，可以按照以下步骤配置证书：

### 方法一：使用自签名证书

1. **导出 GitLab 服务器的证书**：
   ```powershell
   # 使用浏览器访问 GitLab 服务器，导出证书
   # 1. 点击地址栏的锁图标
   # 2. 点击"证书"
   # 3. 选择"详细信息"标签
   # 4. 点击"复制到文件"
   # 5. 选择"Base-64 encoded X.509 (.CER)"
   ```

2. **将证书添加到 Windows 证书存储**：
   ```powershell
   # 以管理员身份运行 PowerShell
   Import-Certificate -FilePath "C:\path\to\your\gitlab.crt" -CertStoreLocation Cert:\LocalMachine\Root
   ```

3. **配置 GitLab Runner 使用证书**：
   ```powershell
   # 创建配置文件目录
   mkdir "C:\GitLab-Runner\certs"
   
   # 复制证书到配置目录
   copy "C:\path\to\your\gitlab.crt" "C:\GitLab-Runner\certs\ca.crt"
   
   # 注册时指定证书
   .\gitlab-runner.exe register --tls-ca-file "C:\GitLab-Runner\certs\ca.crt"
   ```

### 方法二：禁用证书验证（不推荐用于生产环境）

1. **修改 GitLab Runner 配置**：
   ```powershell
   # 编辑配置文件
   notepad "C:\GitLab-Runner\config.toml"
   ```

2. **添加以下配置**：
   ```toml
   [[runners]]
     [runners.custom]
       tls-ca-file = ""
       tls-verify = false
   ```

### 方法三：使用系统代理（如果使用代理服务器）

1. **设置环境变量**：
   ```powershell
   # 设置代理环境变量
   [Environment]::SetEnvironmentVariable("HTTP_PROXY", "http://proxy.example.com:8080", "Machine")
   [Environment]::SetEnvironmentVariable("HTTPS_PROXY", "http://proxy.example.com:8080", "Machine")
   
   # 重启 GitLab Runner 服务
   Restart-Service gitlab-runner
   ```

### 验证证书配置

```powershell
# 测试与 GitLab 服务器的连接
Invoke-WebRequest -Uri "https://git.xxx.com/api/v4/version" -UseBasicParsing
```

### 常见证书错误解决

1. **证书链不完整**：
   ```powershell
   # 导出完整的证书链
   # 使用浏览器导出所有中间证书和根证书
   ```

2. **证书过期**：
   ```powershell
   # 检查证书有效期
   $cert = Get-ChildItem -Path Cert:\LocalMachine\Root | Where-Object {$_.Subject -like "*git.xxx.com*"}
   $cert.NotAfter
   ```

3. **证书名称不匹配**：
   ```powershell
   # 检查证书主题名称
   $cert = Get-ChildItem -Path Cert:\LocalMachine\Root | Where-Object {$_.Subject -like "*git.xxx.com*"}
   $cert.Subject
   ```

## 常见问题解决

1. **权限问题**：
   ```powershell
   # 以管理员身份运行PowerShell
   Set-ExecutionPolicy RemoteSigned
   ```

2. **服务无法启动**：
   ```powershell
   # 检查日志
   Get-EventLog -LogName Application -Source "gitlab-runner" -Newest 10
   ```

3. **更新 Runner**：
   ```powershell
   # 停止服务
   .\gitlab-runner.exe stop
   
   # 下载新版本
   # 替换旧版本
   
   # 重启服务
   .\gitlab-runner.exe start
   ```

## 验证安装

```powershell
# 检查版本
.\gitlab-runner.exe --version

# 检查运行状态
.\gitlab-runner.exe status

# 查看已注册的Runner
.\gitlab-runner.exe list
```

## 卸载 Runner

```powershell
# 停止服务
.\gitlab-runner.exe stop

# 卸载服务
.\gitlab-runner.exe uninstall

# 删除安装目录
Remove-Item -Recurse -Force "C:\GitLab-Runner"
```

## 注意事项

1. 始终以管理员身份运行 PowerShell
2. 确保 Windows 防火墙允许 GitLab Runner 的通信
3. 如果使用 Docker 执行器，确保已安装 Docker Desktop
4. 定期检查 Runner 的日志文件（通常在 `C:\GitLab-Runner\logs`）
5. 确保 GitLab Runner 版本与 GitLab 服务器版本兼容 


在 **Windows Server 2022** 中，添加信任证书（如根证书或自签名证书）的步骤如下：

---

### **方法 1：通过图形界面添加**
1. **打开证书管理控制台**  
   - 按 `Win + R`，输入 `certlm.msc`（管理当前用户的证书）或 `certmgr.msc`（管理计算机范围的证书），按回车。  
   - **注意**：若需全局生效，建议使用 `certlm.msc`（计算机证书存储）。

2. **导入证书**  
   - 展开 **"受信任的根证书颁发机构"** → 右键 **"证书"** → 选择 **"所有任务"** → **"导入"**。

3. **选择证书文件**  
   - 点击 **"下一步"** → **"浏览"**，选择证书文件（如 `.cer` 或 `.crt` 格式），按向导完成导入。

4. **验证添加结果**  
   - 在 **"受信任的根证书颁发机构" → "证书"** 列表中，确认证书已存在。

---

### **方法 2：通过命令行（PowerShell 或 CMD）**
1. **以管理员身份运行 PowerShell**  
   - 右键点击 PowerShell 图标，选择 **"以管理员身份运行"**。

2. **使用 `Import-Certificate` 命令**  
   ```powershell
   Import-Certificate -FilePath "C:\path\to\certificate.cer" -CertStoreLocation Cert:\LocalMachine\Root
   ```
   - 将 `C:\path\to\certificate.cer` 替换为实际证书路径。

3. **验证是否成功**  
   ```powershell
   Get-ChildItem -Path Cert:\LocalMachine\Root | Where-Object { $_.Subject -like "*证书名称*" }
   ```

---

### **方法 3：通过 certutil 工具**
1. **以管理员身份运行 CMD**  
   ```cmd
   certutil -addstore -f "Root" C:\path\to\certificate.cer
   ```

---

### **注意事项**
- **权限要求**：操作计算机证书存储需管理员权限。
- **证书格式**：确保证书文件为 `.cer` 或 `.crt` 格式，若为 `.pfx`，需先导出公钥。
- **作用范围**：
  - **LocalMachine（计算机）**：对所有用户生效。
  - **CurrentUser（当前用户）**：仅对当前用户生效。
- **重启服务**：部分服务（如 IIS）可能需要重启才能加载新证书。

---

### **常见问题**
- **证书不受信任**：确保证书链完整（如中间证书已安装）。
- **证书冲突**：若重复导入同名证书，系统会覆盖旧证书。

按需选择上述方法，即可完成证书信任配置！

# 使用 Chocolatey 安装
choco install 7zip

# 或下载安装包手动安装
# 下载地址：https://www.7-zip.org/download.html