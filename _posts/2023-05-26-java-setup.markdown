---
layout: post
title: Java 开发环境配置指南 Windows
---
### JDK 工具包
#### JDK 和 Java 的关系

![jdk](/assets/java-setup/jdk.png)
[source]

#### 检查安装
通过在命令行输入 `java --version` 可以检查是否已经安装完毕

如果安装过了，会出现类似下图的结果，可以跳过下面的安装步骤

![check.png](/assets/java-setup/check.png)

#### 安装步骤
通过官网 [oracle.com] 下载工具包

![download](/assets/java-setup/download.png)

运行 .exe 文件，选择安装目录，默认安装在 C:\Program Files\Java\jdk-20 下

在电脑设置里，搜索环境变量，选择编辑系统变量

![search.png](/assets/java-setup/search.png)

![entry.png](/assets/java-setup/entry.png)

新建系统变量，JAVA_HOME = C:\Program Files\Java\jdk-20 

![java_home.png](/assets/java-setup/java_home.png)

![add.png](/assets/java-setup/add.png)

编辑系统变量里的 PATH

![path.png](/assets/java-setup/path.png)

选择新建，输入 %JAVA_HOME%\bin，点击确定

![add_to_path.png](/assets/java-setup/add_to_path.png)

打开一个新的终端，确保环境变量生效，输入 `java --version` 检查安装结果

### Java IDE -  IntelliJ IDEA
to be continued...

### Maven
to be continued...

### Spring Boot
to be continued...



[oracle.com]: https://www.oracle.com/java/technologies/downloads/#jdk20-windows
[source]: https://www.infoworld.com/article/3296360/what-is-the-jdk-introduction-to-the-java-development-kit.html
