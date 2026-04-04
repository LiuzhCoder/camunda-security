# 关于xml的理解

> XML 是相当自由的，它的全称是 Extensible 可拓展的 Markup 标记 Language语言。可拓展，指的是标签和标签中的属性可以自定义，标记指的就是标签和属性。

## 1. 定义的部分

所有的语言都有它的一套规则，XML 也不例外，我们先了解“不自由的”部分，那么剩下的都是可以自定义的自由的部分了！

### 1.1 XML的声明

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

这一行是xml文件的声明，有以下要求：

* 可以不写，默认是这个，但是写了就必须在第一行，且前面不能有空行/空格

* 不能写在中间和末尾

#### 为什么？

因为这是XML的规范，为了能让解析器顺利的解析XML文件，就必须遵循统一的规范来写。

#### 为什么是 `<?`

为了区分普通的标签，标记这是一条处理指令，不是一个普通的标签

#### 为什么要区分指令标签和普通标签？

为了方便解析器，对于普通标签，解析器直接将数据缓存，方便后续的操作。指令则直接当场执行了。第一行的含义就是 使用1.0版本的规则和UTF-8的格式去解析。XML只有这一条指令。。。。。。

### 1.2 标签和属性

xml 没有定义任何标签，只定义了属性

* xmlns 命名空间前缀规范

* 以 xml: 开头的保留属性
  
  * xml:lang、xml:space 这些官方保留专属属性 

### 1.3 规则

#### 1.3.1 XML 的规则只有一条，整个文档必须要有一个最外层的父标签，包裹所有的内容

```xml
<parent>
  <!-- 所有其他标签必须放在这里 -->
</parent>
```

注：上面的 parent 标签也是自定义的，任何比爱钱都可以

#### 1.3.2标签必须正确闭合

- 成对出现：`<tag>内容</tag>`
- 自闭合标签：`<tag/>`（结尾必须带斜杠）

#### 1.3.3区分大小写

`<User>` 和 `<user>` 是两个完全不同的标签。

#### 1.3.4 属性值必须加引号

- 正确：`<user id="123">`
- 错误：`<user id=123>`

#### 1.3.5 特殊字符需转义

- `&lt;` 代表 `<`
- `&gt;` 代表 `>`
- `&amp;` 代表 `&`

# 2.maven 中的 pom.xml 解析

项目中的 pom 文件就是xml 格式的，与 xml 真正相关的就两个点

* 第一行的xml声明

* 以及一些xml属性
  
  * xmlns
  
  * xml:xsi
  
  * xml:schemaLocation

第一行已经解释过了，那么接下来解释这些属性

## 2.1 xmlns 命名空间

第一行声明告诉解析器是xml文件，这个其实就是更细分一点，告诉解析器这个xml文件类型下面的maven规则的文件，以下所有的标签都遵循maven规则。

#### 为什么选择域名作为命名空间的值

1. 利用域名全球唯一性，彻底杜绝命名空间重复

2. 网址只是纯文本唯一，不用联网、不下载

#### xmlns：xsi

 xmlns:xsi 是一个命名空间声明，目的是为了能合法使用 xsi:schemaLocation 等 W3C 标准属性，从而让 IDE 和工具知道去哪里找 Schema 文件来验证 XML 格式。

#### xsi:schemaLocation

它是一个映射关系，格式是成对出现的：

  xsi:schemaLocation="命名空间URI    Schema文件地址"

URI 只是一个唯一 Key，通常是命名空间的URI，不会真的去访问，真正去访问的去 Schema（.xsd文件），它定义了这个xml文件有哪些标签和属性

### 2.2 常用标签解释

#### 项目坐标

* project：根标签

* groupId：组织/公司标志，通常反向域名（为什么？）

* artifactId：项目名称/模块名

* version：版本号

* packaging：打包类型 jar war pom，默认 jar

#### 项目信息

name：项目展示名称

description：项目表述

url：项目主页

#### 依赖管理

* dependencies：依赖父目录
  
  * dependency：单个依赖
  
  * scope：依赖作用域
    
    * compile：默认，编译/测试/运行时均有效
    
    * test：仅测试时有效
    
    * provided：编译时有效，运行时由容器提供（如 Servlet API）
    
    * runtime：运行时有效，编译时不需要，如 JDBC
    
    * system：类似 provided ，徐配合 systemPath 指定本地路径
  
  * optional：是否可选
  
  * exclusions：排除的依赖集合
  
  * exclusion：排除的单个依赖

#### 依赖版本统一管理

* dependencyManagement：父路径
  
  * dependencies：需要管理的依赖

#### 构建配置

* build：构建父目录
  
  * finalName：打包文件名
  
  * sourceDirectory：源码路径
  
  * testSourceDirectory：测试代码源码路径
  
  * resources：资源标签
    
    * resource 单个资源
      
      * directory：资源目录
      
      * filtering：是否替换占位符 ${}

#### 插件配置

* plugin：插件配置父目录
  
  * configuration：
    
    * source
    
    * taget
  
  * executions
    
    * execution
    
    * id
    
    * phase 绑定的声明周期
    
    * goals
      
      * goals

#### 多模块 / 继承

* parent：声明父 POM

* modules：父项目中声明子模块列

* relativePath：父 POM 相对路径，默认 ../pom.xml

#### 属性与变量

类似于 java 中的变量，方便统一管理

* properties：属性
  
  * java.version：java版本
  
  * spring.version：spring版本