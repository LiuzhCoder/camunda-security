# 1.maven的setting文件

Maven 有两个重要的配置位置，它们有不同的作用和使用场景：

## 1.1 Maven 安装目录（全局配置）

### 位置

- Windows: C:\apache-maven-3.9.6\conf\settings.xml
- Linux/Mac: /usr/local/apache-maven-3.9.6/conf/settings.xml

## 1.2 用户目录（用户配置）

### 位置

- Windows: C:\Users\用户名\.m2\settings.xml
- Linux/Mac: ~/.m2/settings.xml

## 1.3 优先级

* 命令行指定

* 用户目录

* 安装目录

# 2. Maven 的原理

## 2.1 maven执行流程

```textile
用户输入命令
    ↓
命令解析
    ↓
读取配置文件
    ↓
构建项目模型
    ↓
依赖解析
    ↓
确定生命周期阶段
    ↓
插件绑定
    ↓
执行构建
    ↓
生成产物
```

### 命令解析

将 mvn 命令解析成指令并执行

```bash
用户输入：mvn clean install

Maven 解析：
├── 命令：mvn
├── 生命周期阶段：clean
└── 生命周期阶段：install
```

### 加载配置文件

```textile
1. 读取 settings.xml
   ├── 检查用户目录：~/.m2/settings.xml
   └── 检查安装目录：M2_HOME/conf/settings.xml

2. 解析配置内容
   ├── 本地仓库位置
   ├── 镜像配置
   ├── 代理配置
   └── 认证信息
```

### 构建项目模型

```textile
1. 读取 pom.xml
   ├── 解析项目基本信息
   ├── 解析依赖声明
   ├── 解析插件配置
   └── 解析构建配置

2. 构建依赖树
   ├── 解析直接依赖
   ├── 解析传递性依赖
   └── 处理依赖冲突

3. 创建 Maven Project 对象
   └── 包含项目的所有信息
```

### 依赖解析

```textile
1. 遍历所有依赖
   ↓
2. 检查本地仓库
   ├── 存在 → 验证完整性
   └── 不存在 → 标记为需要下载
   ↓
3. 下载缺失的依赖
   ├── 从镜像下载
   ├── 从中央仓库下载
   └── 从私服下载
   ↓
4. 构建依赖树
   ├── 处理依赖冲突
   │   ├── 最短路径优先
   │   └── 先声明优先
   └── 确定最终依赖列表
```

### 确定声明周期阶段

其实就是看项目要到哪一步，默认是 install

```textile
用户命令：mvn clean install

执行顺序：
1. Clean 生命周期
   ├── pre-clean
   ├── clean
   └── post-clean

2. Default 生命周期（到 install 为止）
   ├── validate
   ├── generate-sources
   ├── process-sources
   ├── generate-resources
   ├── process-resources
   ├── compile
   ├── process-classes
   ├── generate-test-sources
   ├── process-test-sources
   ├── generate-test-resources
   ├── process-test-resources
   ├── test-compile
   ├── process-test-classes
   ├── test
   ├── prepare-package
   ├── package
   ├── pre-integration-test
   ├── integration-test
   ├── post-integration-test
   ├── verify
   └── install
```

### 插件绑定

```textile
每个生命周期阶段绑定到插件目标：

validate → maven-validate-plugin:validate
compile → maven-compiler-plugin:compile
test → maven-surefire-plugin:test
package → maven-jar-plugin:jar
install → maven-install-plugin:install
deploy → maven-deploy-plugin:deploy
```

### 执行构建

```textile
按顺序执行每个阶段：

1. validate 阶段
   └── 验证项目结构正确性

2. compile 阶段
   ├── 创建 target/classes 目录
   ├── 编译 src/main/java
   └── 复制 src/main/resources

3. test-compile 阶段
   ├── 创建 target/test-classes 目录
   ├── 编译 src/test/java
   └── 复制 src/test/resources

4. test 阶段
   ├── 运行单元测试
   └── 生成测试报告

5. package 阶段
   ├── 打包编译结果
   ├── 生成 jar/war 文件
   └── 输出到 target 目录

6. install 阶段
   └── 将打包文件安装到本地仓库
```

## 

### 
