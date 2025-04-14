# GitLab Runner Red Hat Linux 安装指南

本文档提供了在 Red Hat Linux 系统上安装 GitLab Runner 的详细说明。

## 系统要求

- Red Hat Enterprise Linux 7 或更高版本
- root 或 sudo 权限
- 至少 512MB 内存
- 至少 1GB 磁盘空间

## 安装方法

### 方法一：使用 RPM 包安装（推荐）

1. **添加 GitLab Runner 仓库**：

   ```bash
   # 创建仓库配置文件
   cat > /etc/yum.repos.d/gitlab-runner.repo << EOF
   [gitlab-runner]
   name=gitlab-runner
   baseurl=https://packages.gitlab.com/runner/gitlab-runner/el/7/\$basearch
   repo_gpgcheck=1
   gpgcheck=1
   enabled=1
   gpgkey=https://packages.gitlab.com/runner/gitlab-runner/gpgkey
   sslverify=1
   sslcacert=/etc/pki/tls/certs/ca-bundle.crt
   EOF
   ```

2. **安装 GitLab Runner**：

   ```bash
   # 更新包索引
   yum update

   # 安装 GitLab Runner
   yum install gitlab-runner
   ```

### 方法二：手动下载 RPM 包安装

1. **下载 RPM 包**：

   ```bash
   # 下载指定版本的 RPM 包
   wget https://packages.gitlab.com/runner/gitlab-runner/packages/ol/7/gitlab-runner-version-1.x86_64.rpm
   ```

2. **安装 RPM 包**：

   ```bash
   # 安装下载的 RPM 包
   rpm -i gitlab-runner-version-1.x86_64.rpm
   ```

## 注册 Runner

安装完成后，需要注册 Runner：

```bash
# 注册 Runner
gitlab-runner register
```

在注册过程中，你需要提供：
1. GitLab 实例的 URL
2. 注册令牌（从 GitLab 项目的 Settings > CI/CD > Runners 获取）
3. Runner 的描述
4. Runner 的标签
5. 执行器类型（推荐使用 shell 或 docker）

## 配置 Runner 作为服务

```bash
# 启动服务
systemctl start gitlab-runner

# 设置开机自启
systemctl enable gitlab-runner

# 检查服务状态
systemctl status gitlab-runner
```

## 常见问题解决

1. **权限问题**：
   ```bash
   # 确保 gitlab-runner 用户有正确的权限
   usermod -aG docker gitlab-runner
   ```

2. **服务无法启动**：
   ```bash
   # 检查服务日志
   journalctl -u gitlab-runner
   ```

3. **更新 Runner**：
   ```bash
   # 更新 GitLab Runner
   yum update gitlab-runner
   
   # 重启服务
   systemctl restart gitlab-runner
   ```

## 验证安装

```bash
# 检查版本
gitlab-runner --version

# 检查运行状态
gitlab-runner status

# 查看已注册的Runner
gitlab-runner list
```

## 卸载 Runner

```bash
# 停止服务
systemctl stop gitlab-runner

# 禁用服务
systemctl disable gitlab-runner

# 卸载 GitLab Runner
yum remove gitlab-runner

# 清理配置文件
rm -rf /etc/gitlab-runner
rm -rf /home/gitlab-runner
```

## 注意事项

1. 确保系统防火墙允许 GitLab Runner 的通信
2. 如果使用 Docker 执行器，确保已安装 Docker
3. 定期检查 Runner 的日志文件（通常在 `/var/log/gitlab-runner/`）
4. 确保 GitLab Runner 版本与 GitLab 服务器版本兼容
5. 建议使用 SELinux 的宽容模式（permissive）进行测试
6. 如果使用 Docker 执行器，确保 gitlab-runner 用户有正确的 Docker 权限

## SELinux 配置（可选）

如果使用 SELinux，可能需要以下配置：

```bash
# 检查 SELinux 状态
sestatus

# 临时设置为宽容模式
setenforce 0

# 永久设置为宽容模式（需要重启）
sed -i 's/SELINUX=enforcing/SELINUX=permissive/g' /etc/selinux/config
```

## 日志查看

```bash
# 查看实时日志
journalctl -u gitlab-runner -f

# 查看最近 100 行日志
journalctl -u gitlab-runner -n 100
``` 