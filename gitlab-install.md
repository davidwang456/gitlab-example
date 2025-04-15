# GitLab 社区版 Windows Docker Desktop 安装指南

本文档提供了在 Windows Docker Desktop 中安装 GitLab 社区版的详细步骤。

## 系统要求

- Windows 10/11 专业版或企业版
- Docker Desktop for Windows
- 至少 4GB RAM（推荐 8GB 或更多）
- 至少 20GB 可用磁盘空间

## 安装步骤

### 1. 安装 Docker Desktop

1. **下载 Docker Desktop**：
   - 访问 Docker 官网：https://www.docker.com/products/docker-desktop
   - 下载 Windows 版本安装包

2. **安装 Docker Desktop**：
   - 运行下载的安装程序
   - 按照安装向导完成安装
   - 安装完成后启动 Docker Desktop

3. **验证 Docker 安装**：
   ```powershell
   # 检查 Docker 版本
   docker --version
   
   # 检查 Docker 运行状态
   docker info
   ```

### 2. 配置 Docker Desktop

1. **设置资源限制**：
   - 打开 Docker Desktop 设置
   - 在 "Resources" 选项卡中：
     - 设置内存限制为至少 4GB
     - 设置 CPU 核心数
     - 设置磁盘镜像大小

2. **配置 WSL 2（如果使用）**：
   ```powershell
   # 启用 WSL 2
   wsl --set-default-version 2
   
   # 安装 WSL 2 内核更新包
   # 下载地址：https://aka.ms/wsl2kernel
   ```

### 3. 创建 Docker 网络

```powershell
# 创建自定义网络
docker network create gitlab-network
```

### 4. 创建数据卷

```powershell
# 创建数据卷
docker volume create gitlab-config
docker volume create gitlab-data
docker volume create gitlab-logs
```

### 5. 启动 GitLab 容器

```powershell
# 启动 GitLab 容器
docker run --detach `
  --hostname gitlab.example.com `
  --publish 443:443 --publish 8888:80 --publish 22:22 `
  --name gitlab `
  --restart always `
  --volume gitlab-config:/etc/gitlab `
  --volume gitlab-logs:/var/log/gitlab `
  --volume gitlab-data:/var/opt/gitlab `
  --network gitlab-network `
  --shm-size 256m `
  gitlab/gitlab-ce:latest
```

### 6. 配置 GitLab

1. **等待初始化完成**：
   ```powershell
   # 查看容器日志
   docker logs -f gitlab
   ```

2. **修改配置文件**：
   ```powershell
   # 进入容器
   docker exec -it gitlab /bin/bash
   
   # 编辑配置文件
   vi /etc/gitlab/gitlab.rb
   ```

3. **基本配置示例**：
   ```ruby
   external_url 'http://gitlab.example.com:8888'
   gitlab_rails['gitlab_shell_ssh_port'] = 22
   nginx['listen_port'] = 80
   ```

4. **应用配置**：
   ```powershell
   # 在容器内执行
   gitlab-ctl reconfigure
   gitlab-ctl restart
   ```

### 7. 访问 GitLab

1. **获取初始密码**：
   ```powershell
   # 查看初始 root 密码
   docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
   ```

2. **登录 GitLab**：
   - 打开浏览器访问：http://localhost:8888
   - 使用用户名：root
   - 使用初始密码登录

### 8. 基本安全配置

1. **修改 root 密码**：
   - 登录后立即修改 root 密码
   - 设置强密码策略

2. **配置 HTTPS**：
   ```ruby
   # 在 gitlab.rb 中添加
   external_url 'https://gitlab.example.com'
   nginx['redirect_http_to_https'] = true
   ```

### 9. 备份和恢复

1. **创建备份**：
   ```powershell
   # 在容器内执行
   gitlab-backup create
   ```

2. **恢复备份**：
   ```powershell
   # 在容器内执行
   gitlab-backup restore BACKUP=timestamp
   ```

### 10. 维护和更新

1. **更新 GitLab**：
   ```powershell
   # 停止容器
   docker stop gitlab
   
   # 删除容器
   docker rm gitlab
   
   # 拉取新版本
   docker pull gitlab/gitlab-ce:latest
   
   # 使用相同命令重新启动
   ```

2. **日常维护**：
   ```powershell
   # 检查 GitLab 状态
   docker exec -it gitlab gitlab-ctl status
   
   # 查看日志
   docker exec -it gitlab gitlab-ctl tail
   ```

## 常见问题解决

### 1. 容器无法启动

```powershell
# 检查日志
docker logs gitlab

# 检查端口冲突
netstat -ano | findstr "8888"
```

### 2. 性能问题

```