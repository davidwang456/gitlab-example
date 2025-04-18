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

## Python3 和 pip 安装指南

### 方法一：使用 yum 安装（推荐）

```bash
# 安装 Python3
yum install python3

# 安装 pip
yum install python3-pip

# 验证安装
python3 --version
pip3 --version

# 升级 pip
pip3 install --upgrade pip
```

### 方法二：使用源码编译安装

```bash
# 安装编译依赖
yum groupinstall "Development Tools"
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel

# 下载 Python 源码
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
tar xzf Python-3.9.16.tgz
cd Python-3.9.16

# 配置和编译
./configure --enable-optimizations
make altinstall

# 验证安装
python3.9 --version

# 安装 pip
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3.9 get-pip.py

# 验证 pip 安装
pip3.9 --version
```

### 方法三：使用 Miniconda 安装

```bash
# 下载 Miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

# 安装 Miniconda
bash Miniconda3-latest-Linux-x86_64.sh

# 激活环境
source ~/.bashrc

# 验证安装
conda --version
python --version
pip --version
```

### 配置 pip 镜像源（可选）

```bash
# 创建 pip 配置文件目录
mkdir -p ~/.pip

# 配置清华镜像源
cat > ~/.pip/pip.conf << EOF
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
trusted-host = pypi.tuna.tsinghua.edu.cn
EOF
```

### 常见问题解决

1. **pip 命令未找到**：
   ```bash
   # 创建软链接
   ln -s /usr/bin/pip3 /usr/bin/pip
   ```

2. **pip 安装包时权限问题**：
   ```bash
   # 使用 --user 参数安装到用户目录
   pip3 install --user package_name
   
   # 或者使用虚拟环境
   python3 -m venv myenv
   source myenv/bin/activate
   pip install package_name
   ```

3. **更新 pip**：
   ```bash
   pip3 install --upgrade pip
   ```

4. **清理 pip 缓存**：
   ```bash
   pip3 cache purge
   ```

### 离线安装 Python 和 pip

#### 1. 准备离线安装包

在有网络的环境中准备安装包：

```bash
# 创建离线安装包目录
mkdir -p /tmp/offline-python
cd /tmp/offline-python

# 下载 Python 源码包
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz

# 下载 pip 安装脚本
wget https://bootstrap.pypa.io/get-pip.py

# 下载编译依赖包
yum install --downloadonly --downloaddir=./rpm-packages \
    zlib-devel bzip2-devel openssl-devel ncurses-devel \
    sqlite-devel readline-devel tk-devel gdbm-devel \
    db4-devel libpcap-devel xz-devel gcc make

# 打包所有文件
tar czf python-offline-install.tar.gz *
```

#### 2. 离线环境安装步骤

将打包好的文件传输到离线环境后：

```bash
# 解压安装包
tar xzf python-offline-install.tar.gz
cd python-offline-install

# 安装编译依赖
rpm -ivh rpm-packages/*.rpm

# 编译安装 Python
tar xzf Python-3.9.16.tgz
cd Python-3.9.16
./configure --enable-optimizations
make altinstall
cd ..

# 安装 pip
python3.9 get-pip.py

# 验证安装
python3.9 --version
pip3.9 --version
```

#### 3. 离线安装 pip 包

在有网络的环境中准备 pip 包：

```bash
# 创建离线包目录
mkdir -p /tmp/offline-pip
cd /tmp/offline-pip

# 下载需要的包及其依赖
pip3 download -r requirements.txt

# 打包所有包
tar czf pip-packages.tar.gz *
```

在离线环境中安装 pip 包：

```bash
# 解压包
tar xzf pip-packages.tar.gz

# 安装所有包
pip3 install --no-index --find-links=./ *.whl
```

#### 4. 使用本地 PyPI 镜像（可选）

在有网络的环境中：

```bash
# 安装 pip2pi
pip3 install pip2pi

# 创建本地镜像
mkdir -p /tmp/pypi-mirror
pip2pi /tmp/pypi-mirror -r requirements.txt

# 打包镜像
tar czf pypi-mirror.tar.gz /tmp/pypi-mirror
```

在离线环境中：

```bash
# 解压镜像
tar xzf pypi-mirror.tar.gz

# 配置 pip 使用本地镜像
cat > ~/.pip/pip.conf << EOF
[global]
index-url = file:///path/to/pypi-mirror/simple
trusted-host = localhost
EOF
```

#### 5. 注意事项

1. 确保离线环境有足够的磁盘空间
2. 检查系统架构是否匹配（x86_64 或 aarch64）
3. 注意依赖包的版本兼容性
4. 建议在安装前备份重要数据
5. 如果遇到权限问题，可能需要使用 sudo
6. 安装完成后建议验证所有功能是否正常 

## Alpine Linux apk 包管理器常用命令

### 1. 包管理基础命令

```bash
# 更新包索引
apk update

# 升级所有已安装的包
apk upgrade

# 安装包
apk add package_name

# 安装多个包
apk add package1 package2 package3

# 删除包
apk del package_name

# 搜索包
apk search package_name

# 查看包信息
apk info package_name

# 列出已安装的包
apk info
```

### 2. 包缓存管理

```bash
# 清理包缓存
apk cache clean

# 显示缓存大小
apk cache -v

# 下载包但不安装
apk fetch package_name

# 从缓存安装包
apk add --no-cache package_name
```

### 3. 仓库管理

```bash
# 列出所有仓库
apk repo list

# 添加仓库
echo "http://mirrors.aliyun.com/alpine/v3.14/main" >> /etc/apk/repositories

# 更新仓库
apk update
```

### 4. 虚拟包管理

```bash
# 安装虚拟包
apk add --virtual mygroup package1 package2

# 删除虚拟包组
apk del mygroup
```

### 5. 常用开发工具安装

```bash
# 安装编译工具
apk add build-base

# 安装 Python 开发环境
apk add python3 python3-dev py3-pip

# 安装常用开发库
apk add zlib-dev bzip2-dev openssl-dev ncurses-dev sqlite-dev
```

### 6. 系统维护命令

```bash
# 修复损坏的包
apk fix

# 检查包依赖关系
apk audit

# 显示包变更历史
apk history
```

### 7. 常用组合命令

```bash
# 安装并清理缓存
apk add --no-cache package_name

# 更新并升级所有包
apk update && apk upgrade

# 安装开发环境（Python示例）
apk add --no-cache python3 python3-dev py3-pip build-base
```

### 8. 注意事项

1. Alpine Linux 使用 musl libc，与 glibc 不兼容
2. 包名区分大小写
3. 使用 `--no-cache` 可以减小镜像大小
4. 建议定期更新包索引
5. 注意依赖关系，避免破坏系统
6. 使用虚拟包组管理相关包
7. 生产环境建议固定包版本 

## Docker 私有仓库配置

### 1. 配置 DNS 解析

如果私有仓库使用域名，需要确保系统能够正确解析该域名：

```bash
# 检查域名解析
nslookup xxx.xxx

# 如果无法解析，可以添加 hosts 记录
echo "192.168.1.100 xxx.xxx" >> /etc/hosts
```

### 2. 配置 Docker 信任私有仓库

```bash
# 创建或编辑 Docker 配置文件
mkdir -p /etc/docker
cat > /etc/docker/daemon.json << EOF
{
  "insecure-registries": ["xxx.xxx:5000"],
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
EOF

# 重启 Docker 服务
systemctl restart docker
```

### 3. 登录私有仓库

```bash
# 登录私有仓库
docker login xxx.xxx

# 如果使用非标准端口
docker login xxx.xxx:5000

# 使用用户名密码登录
docker login -u username -p password xxx.xxx
```

### 4. 常见问题解决

1. **证书问题**：
   ```bash
   # 如果使用自签名证书，需要将证书添加到系统信任
   mkdir -p /etc/docker/certs.d/xxx.xxx
   cp ca.crt /etc/docker/certs.d/xxx.xxx/ca.crt
   ```

2. **网络问题**：
   ```bash
   # 检查网络连接
   ping xxx.xxx
   
   # 检查端口是否开放
   telnet xxx.xxx 5000
   
   # 检查防火墙设置
   firewall-cmd --list-all
   ```

3. **代理设置**：
   ```bash
   # 如果使用代理，配置 Docker 代理
   mkdir -p /etc/systemd/system/docker.service.d
   cat > /etc/systemd/system/docker.service.d/http-proxy.conf << EOF
   [Service]
   Environment="HTTP_PROXY=http://proxy.example.com:8080/"
   Environment="HTTPS_PROXY=http://proxy.example.com:8080/"
   Environment="NO_PROXY=localhost,127.0.0.1,xxx.xxx"
   EOF
   
   # 重新加载配置
   systemctl daemon-reload
   systemctl restart docker
   ```

### 5. 验证配置

```bash
# 检查 Docker 配置
docker info

# 尝试拉取镜像
docker pull xxx.xxx/ddd:1.0.0

# 查看本地镜像
docker images
```

### 6. 注意事项

1. 确保私有仓库地址正确
2. 检查网络连接是否正常
3. 验证证书是否有效
4. 确认防火墙设置
5. 检查代理配置（如果有）
6. 确保有足够的磁盘空间
7. 检查 Docker 服务状态
8. 查看 Docker 日志排查问题 