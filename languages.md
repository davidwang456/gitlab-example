# Black Duck Detect 多语言支持指南
官方文档
https://documentation.blackduck.com/zh-CN/bundle/detect/page/properties/detectors/python.html

本文档详细说明 Black Duck Detect 如何针对不同编程语言进行扫描。

## 支持的语言和检测器

### 1. Java 项目
- 使用 Gradle 或 Maven 作为构建工具的项目：
```bash
java -jar synopsys-detect-9.2.0.jar \
    --detect.project.name="项目名称" \
    --detect.project.version.name="版本号" \
    --detect.tools=DETECTOR \
    --detect.detector.buildless=true \
    --detect.source.path="项目路径"
```

### 2. Python 项目
- 使用 pip 或 conda 的项目：
```bash
java -jar synopsys-detect-9.2.0.jar \
    --detect.project.name="项目名称" \
    --detect.project.version.name="版本号" \
    --detect.python.python.path="python" \
    --detect.python.pip.path="pip" \
    --detect.python.requirements.path="" \
    --detect.python.requirements.ignore=true \
    --detect.python.include.pipfile=false \
    --detect.python.include.setup.py=false \
    --detect.python.include.requirements.txt=false \
    --detect.tools=DETECTOR \
    --detect.source.path="项目路径" \
    --blackduck.url="服务器URL" \
    --blackduck.api.token="API Token"
```

### 3. JavaScript/Node.js 项目
- 使用 npm 或 yarn 的项目：
```bash
java -jar synopsys-detect-9.2.0.jar \
    --detect.project.name="项目名称" \
    --detect.project.version.name="版本号" \
    --detect.npm.include.dev.dependencies=true \
    --detect.tools=DETECTOR \
    --detect.source.path="项目路径"
```

### 4. C#/.NET 项目
- 使用 NuGet 的项目：
```bash
java -jar synopsys-detect-9.2.0.jar \
    --detect.project.name="项目名称" \
    --detect.project.version.name="版本号" \
    --detect.nuget.inspector.name=nuget \
    --detect.tools=DETECTOR \
    --detect.source.path="项目路径"
```

## 语言特定检测器说明

### 1. Java 检测器
- 支持 Maven、Gradle、SBT 等构建工具
- 可以分析 pom.xml、build.gradle 等文件
- 支持分析 JAR 文件

### 2. Python 检测器
- 支持 pip、conda、poetry 等包管理器
- 可以分析 requirements.txt、setup.py、Pipfile 等文件
- 支持虚拟环境

### 3. JavaScript 检测器
- 支持 npm、yarn 等包管理器
- 可以分析 package.json、yarn.lock 等文件
- 支持分析 node_modules 目录

### 4. C# 检测器
- 支持 NuGet 包管理器
- 可以分析 .csproj、packages.config 等文件
- 支持分析 .NET Core 和 .NET Framework 项目

## 通用参数
```bash
--blackduck.url="Black Duck服务器URL" \
--blackduck.api.token="API Token" \
--detect.risk.report.pdf=true \
--detect.notices.report=true
```

## 最佳实践

### 1. 多语言项目
- 如果项目包含多种语言，Detect 会自动检测并扫描所有支持的语言
- 可以使用 `--detect.tools` 参数指定特定的检测器

### 2. 构建工具检测
- Detect 会自动检测项目使用的构建工具
- 可以使用 `--detect.detector.buildless=true` 跳过构建过程

### 3. 依赖分析
- 使用 `--detect.excluded.directories` 排除不需要扫描的目录
- 使用 `--detect.included.directories` 指定需要扫描的目录

### 4. 报告生成
- 使用 `--detect.risk.report.pdf=true` 生成风险报告
- 使用 `--detect.notices.report=true` 生成许可证报告

### 5. 性能优化
- 使用 `--detect.parallel.processors` 设置并行处理数
- 使用 `--detect.memory` 设置内存限制

## 多语言项目示例
```bash
java -jar synopsys-detect-9.2.0.jar \
    --detect.project.name="多语言项目" \
    --detect.project.version.name="1.0.0" \
    --detect.tools=DETECTOR \
    --detect.python.python.path="python" \
    --detect.python.pip.path="pip" \
    --detect.python.requirements.path="" \
    --detect.python.requirements.ignore=true \
    --detect.python.include.pipfile=false \
    --detect.python.include.setup.py=false \
    --detect.python.include.requirements.txt=false \
    --detect.npm.include.dev.dependencies=true \
    --detect.nuget.inspector.name=nuget \
    --detect.source.path="项目路径" \
    --blackduck.url="服务器URL" \
    --blackduck.api.token="API Token"
```

## 注意事项
1. 确保系统已安装相应语言的运行时环境
2. 确保构建工具（如 Maven、npm 等）已正确安装
3. 对于大型项目，建议增加内存限制
4. 如果遇到特定语言的扫描问题，可以查看详细的日志输出

## 官方文档参考
- [Black Duck Detect 官方文档](https://community.synopsys.com/s/document-item?bundleId=integrations-detect&topicId=properties.html&_LANG=enus)
- [Detect 命令行参数参考](https://community.synopsys.com/s/document-item?bundleId=integrations-detect&topicId=properties.html&_LANG=enus)
- [Detect 语言支持矩阵](https://community.synopsys.com/s/document-item?bundleId=integrations-detect&topicId=supported_languages.html&_LANG=enus)

## Black Duck 组件分析

### 1. Black Duck Hub
- 应用场景：
  - 集中式管理开源组件
  - 提供可视化界面进行组件分析
  - 生成详细的合规性报告
  - 团队协作和审批流程管理
- 特点：
  - 提供 Web 界面
  - 支持多项目集中管理
  - 提供详细的漏洞和许可证分析

### 2. Black Duck Detect
- 应用场景：
  - 命令行工具，适合 CI/CD 集成
  - 自动化扫描和检测
  - 支持多种编程语言和构建工具
  - 生成 BOM（物料清单）
- 特点：
  - 轻量级命令行工具
  - 支持多种语言（如 Java、Python、JavaScript、C# 等）
  - 可以集成到构建流程中

### 3. Black Duck Binary Analysis
- 应用场景：
  - 分析已编译的二进制文件
  - 识别第三方组件
  - 检测已知漏洞
- 特点：
  - 不需要源代码
  - 可以分析编译后的应用程序
  - 支持多种二进制格式

### 4. Black Duck Protex
- 应用场景：
  - 代码片段级别的分析
  - 知识产权保护
  - 代码相似度检测
- 特点：
  - 深入分析代码片段
  - 关注代码相似度和知识产权问题
  - 提供详细的代码匹配报告

## 组件差异对比

### 1. 分析深度
- Hub：项目级别分析
- Detect：组件级别分析
- Binary Analysis：二进制级别分析
- Protex：代码片段级别分析

### 2. 使用方式
- Hub：Web 界面操作
- Detect：命令行工具
- Binary Analysis：独立工具
- Protex：独立工具

### 3. 集成能力
- Hub：提供 API 接口
- Detect：适合 CI/CD 集成
- Binary Analysis：独立运行
- Protex：独立运行

### 4. 主要用途
- Hub：管理和报告
- Detect：自动化扫描
- Binary Analysis：二进制分析
- Protex：代码相似度分析

### 5. 适用场景
- Hub：团队协作和项目管理
- Detect：开发过程中的自动化检查
- Binary Analysis：已编译应用的安全分析
- Protex：知识产权保护

## 建议使用场景

### 1. 开发团队
- 主要使用 Detect 进行日常开发检查
- 使用 Hub 进行项目管理和报告查看

### 2. 安全团队
- 使用 Binary Analysis 分析生产环境应用
- 使用 Hub 进行漏洞管理和跟踪

### 3. 法务团队
- 使用 Protex 进行知识产权分析
- 使用 Hub 进行许可证合规性检查

### 4. DevOps 团队
- 将 Detect 集成到 CI/CD 流程
- 使用 Hub 进行自动化报告生成

这些组件可以单独使用，也可以组合使用，根据具体需求选择合适的工具组合。例如，一个完整的开源组件管理流程可能包括：
1. 使用 Detect 进行日常扫描
2. 使用 Hub 进行管理和报告
3. 使用 Binary Analysis 进行生产环境检查
4. 使用 Protex 进行知识产权验证

## Detect 组件详解：Detectors 和 Inspectors

### 1. Detectors（检测器）
- 应用场景：
  - 用于识别项目使用的包管理器类型
  - 自动检测项目中的依赖关系
  - 支持多种包管理器生态系统
  - 适用于快速扫描和初步分析

- 特点：
  - 更高级别的抽象
  - 自动识别项目类型
  - 支持多种包管理器
  - 不需要特定配置即可运行

- 工作方式：
  - 扫描项目目录
  - 识别包管理器配置文件
  - 提取依赖信息
  - 生成依赖关系图

### 2. Inspectors（检查器）
- 应用场景：
  - 深入分析特定包管理器的内部结构
  - 获取详细的依赖信息
  - 处理复杂的依赖关系
  - 需要精确控制的分析场景

- 特点：
  - 更底层的实现
  - 针对特定包管理器
  - 需要特定配置
  - 提供更详细的分析结果

- 工作方式：
  - 通过 API 访问包管理器
  - 执行深度分析
  - 提取详细的元数据
  - 生成精确的依赖报告

### 主要区别：

1. **抽象级别**：
   - Detectors：高级抽象，自动识别和扫描
   - Inspectors：底层实现，需要特定配置

2. **使用方式**：
   - Detectors：开箱即用，自动运行
   - Inspectors：需要明确指定和配置

3. **分析深度**：
   - Detectors：广度优先，快速扫描
   - Inspectors：深度优先，详细分析

4. **适用场景**：
   - Detectors：日常扫描，快速检查
   - Inspectors：深入分析，精确控制

5. **配置要求**：
   - Detectors：基本不需要配置
   - Inspectors：需要特定配置

6. **输出结果**：
   - Detectors：基本的依赖关系
   - Inspectors：详细的依赖信息和元数据

### 使用建议：

1. **日常开发**：
   - 主要使用 Detectors
   - 快速检查依赖关系
   - 确保基本的合规性

2. **深度分析**：
   - 使用 Inspectors
   - 获取详细的依赖信息
   - 处理复杂的依赖关系

3. **特定场景**：
   - 需要精确控制时使用 Inspectors
   - 需要快速扫描时使用 Detectors

4. **组合使用**：
   - 先用 Detectors 进行初步扫描
   - 对需要深入分析的部分使用 Inspectors

### 实际应用示例：

1. **Java 项目**：
   - Detectors：自动识别 Maven 或 Gradle 项目
   - Inspectors：深入分析 pom.xml 或 build.gradle

2. **Python 项目**：
   - Detectors：识别 requirements.txt 或 setup.py
   - Inspectors：分析虚拟环境中的依赖关系

3. **JavaScript 项目**：
   - Detectors：识别 package.json
   - Inspectors：分析 node_modules 目录

4. **多语言项目**：
   - Detectors：自动识别所有支持的语言
   - Inspectors：针对特定语言进行深入分析

这种分层设计使得 Black Duck Detect 能够灵活应对不同的分析需求，从快速扫描到深度分析都能胜任。

--detect.python.include.patterns="*.py"  # 只扫描 .py 文件
--detect.excluded.directories="tests,docs"  # 排除测试和文档目录 