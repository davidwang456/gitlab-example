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