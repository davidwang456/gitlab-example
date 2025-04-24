# Linux curl 命令使用指南

## 1. 基本下载 JAR 文件

```bash
# 基本下载 JAR 文件
curl -o detect-latest.jar https://example.com/path/to/detect-latest.jar
```

## 2. 带进度显示的下载

```bash
# 显示下载进度
curl -# -o detect-latest.jar https://example.com/path/to/detect-latest.jar
```

## 3. 带断点续传的下载

```bash
# 断点续传（如果下载中断，可以继续下载）
curl -C - -o detect-latest.jar https://example.com/path/to/detect-latest.jar
```

## 4. 带认证的下载

```bash
# 使用 Bearer Token 认证
curl -o detect-latest.jar \
  -H "Authorization: Bearer your_token" \
  https://example.com/path/to/detect-latest.jar

# 使用基本认证
curl -o detect-latest.jar \
  -u username:password \
  https://example.com/path/to/detect-latest.jar
```

## 5. 带超时设置的下载

```bash
# 设置连接超时和传输超时
curl -o detect-latest.jar \
  --connect-timeout 30 \
  --max-time 300 \
  https://example.com/path/to/detect-latest.jar
```

## 6. 带代理的下载

```bash
# 使用 HTTP 代理
curl -o detect-latest.jar \
  -x http://proxy.example.com:8080 \
  https://example.com/path/to/detect-latest.jar

# 使用 SOCKS 代理
curl -o detect-latest.jar \
  --socks5 proxy.example.com:1080 \
  https://example.com/path/to/detect-latest.jar
```

## 7. 带校验的下载

```bash
# 下载 JAR 文件和校验文件
curl -o detect-latest.jar https://example.com/path/to/detect-latest.jar
curl -o detect-latest.jar.sha256 https://example.com/path/to/detect-latest.jar.sha256

# 验证 SHA256 校验和
sha256sum -c detect-latest.jar.sha256
```

## 8. 带重试的下载

```bash
# 失败时重试
curl -o detect-latest.jar \
  --retry 3 \
  --retry-delay 5 \
  https://example.com/path/to/detect-latest.jar
```

## 9. 带限速的下载

```bash
# 限制下载速度（单位：字节/秒）
curl -o detect-latest.jar \
  --limit-rate 100k \
  https://example.com/path/to/detect-latest.jar
```

## 10. 带详细日志的下载

```bash
# 显示详细下载信息
curl -v -o detect-latest.jar \
  https://example.com/path/to/detect-latest.jar
```

## 11. 带自定义请求头的下载

```bash
# 添加自定义请求头
curl -o detect-latest.jar \
  -H "User-Agent: MyDownloader/1.0" \
  -H "Accept: application/java-archive" \
  https://example.com/path/to/detect-latest.jar
```

## 12. 带错误处理的下载

```bash
# 检查下载是否成功
if curl -o detect-latest.jar https://example.com/path/to/detect-latest.jar; then
    echo "下载成功"
else
    echo "下载失败"
    exit 1
fi
```

## 13. 带进度条和详细信息的下载

```bash
# 显示进度条和详细信息
curl -# -v -o detect-latest.jar \
  https://example.com/path/to/detect-latest.jar
```

## 14. 带压缩传输的下载

```bash
# 使用压缩传输
curl -o detect-latest.jar \
  -H "Accept-Encoding: gzip, deflate" \
  https://example.com/path/to/detect-latest.jar
```

## 15. 带 Cookie 的下载

```bash
# 使用 Cookie 下载
curl -o detect-latest.jar \
  -b "session=12345" \
  https://example.com/path/to/detect-latest.jar
```

## 16. 下载并验证 JAR 文件完整性

```bash
# 下载 JAR 文件和签名
curl -o detect-latest.jar https://example.com/path/to/detect-latest.jar
curl -o detect-latest.jar.asc https://example.com/path/to/detect-latest.jar.asc

# 验证签名
gpg --verify detect-latest.jar.asc detect-latest.jar
```

## 17. 下载到特定目录

```bash
# 下载到指定目录
curl -o /path/to/directory/detect-latest.jar \
  https://example.com/path/to/detect-latest.jar
```

## 18. 带重定向跟随的下载

```bash
# 跟随重定向下载
curl -L -o detect-latest.jar \
  https://example.com/path/to/detect-latest.jar
```

## 注意事项

1. 确保有足够的磁盘空间
2. 检查网络连接稳定性
3. 验证下载文件的完整性
4. 注意文件权限设置
5. 考虑使用代理时的网络环境
6. 注意认证信息的保密性
7. 定期更新下载的 JAR 文件
8. 保持下载工具的更新

## 常见问题解决

1. 下载速度慢
   - 检查网络连接
   - 尝试使用代理
   - 调整限速设置

2. 下载中断
   - 使用断点续传功能
   - 增加重试次数
   - 检查网络稳定性

3. 认证失败
   - 验证认证信息
   - 检查 token 是否过期
   - 确认权限设置

4. 文件校验失败
   - 重新下载文件
   - 验证校验和算法
   - 检查文件完整性

5. 代理连接问题
   - 验证代理设置
   - 检查代理服务器状态
   - 尝试其他代理服务器

## Docker 容器和镜像操作指南

### 1. 导出 Docker 容器为文件

#### 1.1 查看当前运行的容器
```bash
# 查看所有运行中的容器
docker ps

# 查看所有容器（包括已停止的）
docker ps -a
```

#### 1.2 导出容器为 tar 文件
```bash
# 导出容器为 tar 文件
docker export <container_id> > container.tar

# 示例
docker export abc123def456 > my_container.tar
```

#### 1.3 导出容器为压缩文件
```bash
# 导出并压缩
docker export <container_id> | gzip > container.tar.gz

# 示例
docker export abc123def456 | gzip > my_container.tar.gz
```

### 2. 将导出的文件重新导入为 Docker 镜像

#### 2.1 从 tar 文件导入
```bash
# 从 tar 文件导入
cat container.tar | docker import - repository:tag

# 示例
cat my_container.tar | docker import - my_image:latest
```

#### 2.2 从压缩文件导入
```bash
# 从压缩文件导入
gunzip -c container.tar.gz | docker import - repository:tag

# 示例
gunzip -c my_container.tar.gz | docker import - my_image:latest
```

#### 2.3 直接导入压缩文件
```bash
# 直接导入压缩文件
docker import container.tar.gz repository:tag

# 示例
docker import my_container.tar.gz my_image:latest
```

### 3. 验证导入的镜像

```bash
# 查看所有镜像
docker images

# 运行新导入的镜像
docker run -it my_image:latest /bin/bash
```

### 4. 注意事项

1. 导出容器时
   - 确保容器已停止或处于稳定状态
   - 检查磁盘空间是否充足
   - 注意文件权限设置

2. 导入镜像时
   - 确保 tar 文件完整性
   - 检查镜像名称和标签
   - 验证导入后的镜像大小

3. 数据持久化
   - 重要数据建议使用数据卷
   - 配置文件建议挂载外部文件
   - 考虑使用 Dockerfile 重建镜像

### 5. 常见问题解决

1. 导出失败
   - 检查容器状态
   - 验证磁盘空间
   - 确认文件权限

2. 导入失败
   - 检查文件完整性
   - 验证文件格式
   - 确认镜像名称格式

3. 运行问题
   - 检查端口映射
   - 验证环境变量
   - 确认数据卷挂载

4. 性能问题
   - 优化镜像大小
   - 使用多阶段构建
   - 清理无用文件

### 6. 最佳实践

1. 导出前准备
   - 停止不必要的服务
   - 清理临时文件
   - 备份重要数据

2. 导入后操作
   - 测试镜像功能
   - 更新标签信息
   - 清理旧版本

3. 安全考虑
   - 检查敏感信息
   - 更新安全补丁
   - 限制容器权限

## Docker 镜像重打标签和推送指南

### 1. 查看现有镜像

```bash
# 查看所有本地镜像
docker images

# 查看特定镜像的详细信息
docker inspect <image_id>
```

### 2. 重打标签（Tag）

```bash
# 基本语法
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

# 示例：将本地镜像重打标签
docker tag local_image:latest new_registry.com/namespace/image:latest

# 示例：指定特定版本
docker tag local_image:1.0 new_registry.com/namespace/image:1.0

# 示例：使用镜像 ID
docker tag abc123def456 new_registry.com/namespace/image:latest
```

### 3. 登录目标仓库

```bash
# 登录 Docker Hub
docker login

# 登录私有仓库
docker login new_registry.com

# 使用用户名密码登录
docker login -u username -p password new_registry.com

# 使用配置文件登录
cat > ~/.docker/config.json << EOF
{
    "auths": {
        "new_registry.com": {
            "auth": "$(echo -n 'username:password' | base64)"
        }
    }
}
EOF
```

### 4. 推送镜像

```bash
# 推送镜像到新仓库
docker push new_registry.com/namespace/image:latest

# 推送特定版本
docker push new_registry.com/namespace/image:1.0

# 推送所有标签
docker push --all-tags new_registry.com/namespace/image
```

### 5. 批量处理多个镜像

```bash
# 使用脚本批量重打标签和推送
#!/bin/bash

# 定义源镜像和目标仓库
SOURCE_IMAGES=("image1:latest" "image2:1.0" "image3:latest")
TARGET_REGISTRY="new_registry.com/namespace"

# 循环处理每个镜像
for image in "${SOURCE_IMAGES[@]}"; do
    # 重打标签
    docker tag "$image" "$TARGET_REGISTRY/${image##*/}"
    # 推送镜像
    docker push "$TARGET_REGISTRY/${image##*/}"
done
```

### 6. 使用 Docker Save 和 Load

```bash
# 保存镜像为 tar 文件
docker save -o image.tar local_image:latest

# 从 tar 文件加载镜像
docker load -i image.tar

# 重打标签并推送
docker tag local_image:latest new_registry.com/namespace/image:latest
docker push new_registry.com/namespace/image:latest
```

### 7. 注意事项

1. **权限管理**
   - 确保有目标仓库的推送权限
   - 检查命名空间访问权限
   - 验证认证信息有效性

2. **镜像大小**
   - 检查镜像大小限制
   - 考虑使用多阶段构建
   - 清理无用层

3. **网络考虑**
   - 检查网络连接稳定性
   - 考虑使用代理
   - 设置适当的超时

4. **安全考虑**
   - 使用 HTTPS
   - 保护认证信息
   - 扫描镜像漏洞

### 8. 常见问题解决

1. **认证失败**
   - 检查登录状态
   - 验证认证信息
   - 清除旧的认证缓存

2. **推送失败**
   - 检查网络连接
   - 验证仓库权限
   - 确认镜像格式

3. **空间不足**
   - 清理无用镜像
   - 检查磁盘空间
   - 优化镜像大小

4. **版本冲突**
   - 检查标签唯一性
   - 处理版本覆盖
   - 备份重要版本

### 9. 最佳实践

1. **镜像管理**
   - 使用语义化版本
   - 保持标签一致性
   - 定期清理旧版本

2. **安全实践**
   - 扫描镜像漏洞
   - 使用签名验证
   - 限制访问权限

3. **性能优化**
   - 使用镜像缓存
   - 优化层结构
   - 减少镜像大小

## curl 上传文件命令示例

### 1. 基本文件上传

```bash
# 上传单个文件
curl -X POST -F "file=@/path/to/local/file.txt" https://example.com/upload

# 上传多个文件
curl -X POST \
  -F "file1=@/path/to/file1.txt" \
  -F "file2=@/path/to/file2.txt" \
  https://example.com/upload
```

### 2. 带认证的文件上传

```bash
# 使用 Bearer Token 认证上传
curl -X POST \
  -H "Authorization: Bearer your_token" \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 使用基本认证上传
curl -X POST \
  -u username:password \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 3. 带进度显示的上传

```bash
# 显示上传进度
curl -# -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 4. 带自定义请求头的上传

```bash
# 添加自定义请求头
curl -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "X-Custom-Header: value" \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 5. 带超时设置的上传

```bash
# 设置上传超时
curl -X POST \
  --connect-timeout 30 \
  --max-time 300 \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 6. 带代理的上传

```bash
# 使用 HTTP 代理上传
curl -X POST \
  -x http://proxy.example.com:8080 \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 使用 SOCKS 代理上传
curl -X POST \
  --socks5 proxy.example.com:1080 \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 7. 带重试机制的上传

```bash
# 失败时重试
curl -X POST \
  --retry 3 \
  --retry-delay 5 \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 8. 带限速的上传

```bash
# 限制上传速度（单位：字节/秒）
curl -X POST \
  --limit-rate 100k \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 9. 带详细日志的上传

```bash
# 显示详细上传信息
curl -v -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 10. 带错误处理的上传

```bash
# 检查上传是否成功
if curl -X POST -F "file=@/path/to/file.txt" https://example.com/upload; then
    echo "上传成功"
else
    echo "上传失败"
    exit 1
fi
```

### 11. 带 Cookie 的上传

```bash
# 使用 Cookie 上传
curl -X POST \
  -b "session=12345" \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 12. 上传大文件

```bash
# 上传大文件（使用分块传输）
curl -X POST \
  --data-binary @/path/to/large_file.bin \
  https://example.com/upload
```

### 13. 上传并保存响应

```bash
# 上传文件并保存响应
curl -X POST \
  -F "file=@/path/to/file.txt" \
  -o response.txt \
  https://example.com/upload
```

### 14. 上传注意事项

1. 文件大小限制
   - 检查服务器端限制
   - 考虑分块上传
   - 注意内存使用

2. 网络稳定性
   - 使用断点续传
   - 设置合理的超时
   - 启用重试机制

3. 安全性
   - 使用 HTTPS
   - 保护认证信息
   - 验证文件类型

4. 性能优化
   - 压缩大文件
   - 使用并行上传
   - 优化网络设置

### 15. 常见问题解决

1. 上传失败
   - 检查文件权限
   - 验证网络连接
   - 确认服务器状态

2. 速度慢
   - 检查网络带宽
   - 优化文件大小
   - 调整限速设置

3. 认证问题
   - 验证认证信息
   - 检查 token 有效期
   - 确认权限设置

4. 格式问题
   - 检查文件格式
   - 验证 MIME 类型
   - 确认编码方式

### 16. 带用户名密码的上传

```bash
# 基本认证方式（用户名:密码）
curl -X POST \
  -u username:password \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 使用环境变量存储密码（更安全）
export USERNAME="your_username"
export PASSWORD="your_password"
curl -X POST \
  -u "$USERNAME:$PASSWORD" \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 使用 .netrc 文件（最安全）
# 首先创建 .netrc 文件
cat > ~/.netrc << EOF
machine example.com
login your_username
password your_password
EOF
# 然后使用 -n 参数
curl -X POST \
  -n \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 使用 URL 编码的用户名密码
curl -X POST \
  -u "$(echo -n 'username:password' | base64)" \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 使用自定义认证头
curl -X POST \
  -H "Authorization: Basic $(echo -n 'username:password' | base64)" \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

### 17. 用户名密码上传注意事项

1. 安全性考虑
   - 避免在命令行直接暴露密码
   - 使用环境变量或配置文件
   - 考虑使用 .netrc 文件
   - 定期更换密码

2. 认证方式选择
   - 基本认证（Basic Auth）
   - 摘要认证（Digest Auth）
   - OAuth 认证
   - 自定义认证头

3. 错误处理
   - 检查认证失败
   - 处理密码过期
   - 验证用户名格式
   - 处理特殊字符

4. 最佳实践
   - 使用 HTTPS
   - 限制密码尝试次数
   - 启用双因素认证
   - 定期审计访问日志

### 18. Unknown Host 错误解决指南

#### 18.1 基本检查步骤

```bash
# 1. 检查域名解析
nslookup example.com
# 或
dig example.com

# 2. 检查网络连接
ping example.com

# 3. 检查 hosts 文件
cat /etc/hosts
```

#### 18.2 解决方案

1. **使用 IP 地址替代域名**
```bash
# 获取服务器 IP 地址
nslookup example.com
# 然后使用 IP 地址
curl -X POST \
  -F "file=@/path/to/file.txt" \
  https://192.168.1.100/upload
```

2. **指定 DNS 服务器**
```bash
# 使用特定 DNS 服务器
curl --dns-servers 8.8.8.8 \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

3. **修改 hosts 文件**
```bash
# 编辑 hosts 文件
sudo nano /etc/hosts
# 添加以下行
192.168.1.100 example.com
```

4. **使用 --resolve 参数**
```bash
# 直接指定域名解析
curl --resolve example.com:443:192.168.1.100 \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

5. **跳过主机名验证**
```bash
# 跳过 SSL 主机名验证
curl -k \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

#### 18.3 网络配置检查

1. **检查 DNS 配置**
```bash
# 查看 DNS 配置
cat /etc/resolv.conf

# 临时修改 DNS
sudo echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

2. **检查网络接口**
```bash
# 查看网络接口状态
ifconfig
# 或
ip addr

# 检查路由表
route -n
# 或
ip route
```

3. **检查防火墙设置**
```bash
# 检查防火墙状态
sudo iptables -L
# 或
sudo ufw status
```

#### 18.4 代理设置

1. **使用代理服务器**
```bash
# 设置代理环境变量
export http_proxy="http://proxy.example.com:8080"
export https_proxy="http://proxy.example.com:8080"

# 或直接在 curl 命令中指定
curl -x http://proxy.example.com:8080 \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

2. **绕过代理**
```bash
# 对特定域名不使用代理
export no_proxy="example.com,localhost"

# 或在 curl 命令中指定
curl --noproxy example.com \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

#### 18.5 常见问题排查

1. **DNS 问题**
   - 检查 DNS 服务器是否可达
   - 验证域名是否正确
   - 检查 DNS 缓存

2. **网络连接问题**
   - 检查网络接口状态
   - 验证路由配置
   - 检查防火墙规则

3. **代理配置问题**
   - 验证代理服务器状态
   - 检查代理认证信息
   - 确认代理规则

4. **SSL/TLS 问题**
   - 检查证书有效性
   - 验证 SSL 版本兼容性
   - 确认加密套件支持

#### 18.6 调试命令

```bash
# 显示详细连接信息
curl -v \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload

# 只显示连接信息
curl --trace-ascii debugdump.txt \
  -X POST \
  -F "file=@/path/to/file.txt" \
  https://example.com/upload
```

#### 18.7 最佳实践

1. **网络配置**
   - 使用可靠的 DNS 服务器
   - 配置适当的超时时间
   - 设置合理的重试机制

2. **安全考虑**
   - 使用 HTTPS
   - 验证证书
   - 保护认证信息

3. **性能优化**
   - 使用连接池
   - 启用压缩
   - 优化 DNS 缓存 