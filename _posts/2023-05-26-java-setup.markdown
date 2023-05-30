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
一位著名上单说过，配置一个让人舒服的 IDE 是成功的一半，[IntelliJ IDEA] 可以说是市面上最火的 Java IDE 了

推荐使用 [JetBrains Toolbox] 管理全家桶，轻松安装卸载升级

#### 全局 SDK
配置 Java SDK (JDK)，具体步骤可以参照[官网说明]
![sdk.png](/assets/java-setup/sdk.png)

#### 以下是我个人习惯的一些 IDE 配置
今年 JetBrains 推出了他们的 Beta 版新 UI，界面非常 neat and clean

![new_ui.png](/assets/java-setup/new_ui.png)

一位著名中单说过，如果一个程序员不用暗色模式，那 TA 不是一个好的程序员

![theme.png](/assets/java-setup/theme.png)

字号调大一点可以保护视力

![font.png](/assets/java-setup/font.png)

修改 PATH

![ide_path.png](/assets/java-setup/ide_path.png)

安装插件

![plugins.png](/assets/java-setup/plugins.png)

- IdeaVim：一位著名辅助说过，好的程序员都用 vim
- GitToolBox：可以用来 git blame 的工具，会在代码的每一行后面显示 commit 的作者和 commit message，非常方便

![git_toolbox.png](/assets/java-setup/git_toolbox.png)

- IKun Progress：可以让你的进度条变成 ikun 打篮球

![ikun.jpg](/assets/java-setup/ikun.jpg)

- WakaTime：记录你写码时间的工具

![wakatime.png](/assets/java-setup/wakatime.png)

一览（要素拉满）
![preview.png](/assets/java-setup/preview.png)

最后，对天空大声说出 Hello World！
![hello_world.png](/assets/java-setup/hello_world.png)

### Maven
to be continued...

### Spring Boot
to be continued...



[oracle.com]: https://www.oracle.com/java/technologies/downloads/#jdk20-windows
[source]: https://www.infoworld.com/article/3296360/what-is-the-jdk-introduction-to-the-java-development-kit.html
[JetBrains Toolbox]: https://www.jetbrains.com/toolbox-app/
[IntelliJ IDEA]: https://www.jetbrains.com/idea/download/#section=windows
[官网说明]: https://www.jetbrains.com/help/idea/sdk.html
