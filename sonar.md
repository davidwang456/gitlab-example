# SonarScanner CLI Windows Server 安装指南

本文档提供了在 Windows Server 上安装 SonarScanner CLI 的详细步骤。

## 系统要求

- Windows Server 2016/2019/2022
- Java 11 或更高版本
- 至少 2GB 可用内存
- 至少 1GB 可用磁盘空间

## 安装步骤

### 1. 安装 Java

1. **下载 Java JDK**：
   - 访问 Oracle 官网或使用 OpenJDK
   - 推荐使用 OpenJDK 17：https://adoptium.net/temurin/releases/

2. **安装 Java**：
   ```powershell
   # 下载 OpenJDK 17
   Invoke-WebRequest -Uri "https://github.com/adoptium/temurin17-binaries/releases/download/jdk-17.0.8%2B7/OpenJDK17U-jdk_x64_windows_hotspot_17.0.8_7.msi" -OutFile "OpenJDK17.msi"

   # 安装 Java
   msiexec /i OpenJDK17.msi /quiet
   ```

3. **验证 Java 安装**：
   ```powershell
   java -version
   javac -version
   ```

### 2. 安装 SonarScanner CLI

1. **下载 SonarScanner**：
   ```powershell
   # 创建安装目录
   New-Item -ItemType Directory -Path "C:\SonarScanner" -Force

   # 下载 SonarScanner
   Invoke-WebRequest -Uri "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856.zip" -OutFile "C:\SonarScanner\sonar-scanner.zip"
   ```

2. **解压文件**：
   ```powershell
   # 使用 Expand-Archive 解压
   Expand-Archive -Path "C:\SonarScanner\sonar-scanner.zip" -DestinationPath "C:\SonarScanner" -Force

   # 重命名目录
   Rename-Item -Path "C:\SonarScanner\sonar-scanner-4.8.0.2856" -NewName "sonar-scanner"
   ```

3. **配置环境变量**：
   ```powershell
   # 设置系统环境变量
   [Environment]::SetEnvironmentVariable("SONAR_SCANNER_HOME", "C:\SonarScanner\sonar-scanner", "Machine")
   [Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\SonarScanner\sonar-scanner\bin", "Machine")

   # 立即生效
   $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
   ```

### 3. 配置 SonarScanner

1. **创建配置文件**：
   ```powershell
   # 创建 sonar-scanner.properties 文件
   @"
   sonar.host.url=http://your-sonarqube-server:9000
   sonar.login=your-sonarqube-token
   sonar.sourceEncoding=UTF-8
   "@ | Out-File -FilePath "C:\SonarScanner\sonar-scanner\conf\sonar-scanner.properties" -Encoding UTF8
   ```

2. **配置项目文件**：
   ```powershell
   # 创建示例项目配置文件
   @"
   sonar.projectKey=my-project
   sonar.projectName=My Project
   sonar.projectVersion=1.0
   sonar.sources=.
   sonar.sourceEncoding=UTF-8
   "@ | Out-File -FilePath "sonar-project.properties" -Encoding UTF8
   ```

### 4. 验证安装

1. **检查版本**：
   ```powershell
   sonar-scanner --version
   ```

2. **运行测试扫描**：
   ```powershell
   # 创建测试项目
   New-Item -ItemType Directory -Path "test-project" -Force
   Set-Location -Path "test-project"
   
   # 创建测试文件
   @"
   public class Test {
       public static void main(String[] args) {
           System.out.println("Hello, SonarQube!");
       }
   }
   "@ | Out-File -FilePath "Test.java" -Encoding UTF8
   
   # 运行扫描
   sonar-scanner
   ```

### 5. 常见问题解决

1. **Java 版本问题**：
   ```powershell
   # 检查 Java 版本
   java -version
   
   # 如果版本不正确，更新 JAVA_HOME
   [Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Java\jdk-17", "Machine")
   
   # 确保使用正确的 Java 版本
   $env:Path = "C:\Program Files\Java\jdk-17\bin;" + $env:Path
   ```

2. **权限问题**：
   ```