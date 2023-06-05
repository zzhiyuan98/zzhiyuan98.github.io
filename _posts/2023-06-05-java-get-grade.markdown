---
layout: post
title: 使用 Spring Boot 和 MySql 写一个简单应用
---
### 剧本介绍
在这篇 walk-through 里面我们会：
1. 使用 MySql 建一个数据库
2. 使用 Sprint Boot 连接数据库并写好一个简单的接口
3. 使用 Postman 调用接口
4. 使用 Swagger 生成接口文档

### MySQL
官网下载安装（米老猫使用的是 Windows 环境，所以就贴 Windows 链接了）

[MySQL Downloads for Windows]

安装过程中会输入 root 账号的密码，这个密码需要记住

安装完毕之后添加系统环境变量 👇（详细步骤可参考 Java 篇安装 JDK）

![mysql-path](/assets/get-grade/mysql-path.png)

连接 MySQL 服务器的命令：

```shell
mysql -h host -u user -p
```

本机运行可以省略 host，这里我们以 root 身份登录，然后输入安装时填写的密码：

```shell
mysql -u root -p
Enter password: ******
```

想要退出的时候输入 quit 回车即可

```shell
mysql> quit
```

以 root 身份新建一个数据库和一个用户，使这个用户拥有该数据库的权限

这里假设我们的数据库是 “学校”，我们想要在数据库里存学生的 id，姓名，和成绩；创建的用户叫 julia，登录密码是 123456

```shell
mysql> create database school;
mysql> create user 'julia'@'%' identified by '123456';
mysql> grant all on school.* to 'julia'@'%';
```

> 1. 命令需要以分号结尾
> 2. "@" 后面跟的是 hostname，省略的时候相当于 "%"，表示该用户可以从任意主机连接服务器
> 3. 第一步创建数据库，第二部创建用户，第三步给予权限

### Spring Boot
TBC

### Postman
TBC

### Swagger
TBC

### Reference
[MySQL Documentation]

[Accessing data with MySQL]

[MySQL Documentation]: https://dev.mysql.com/doc/
[MySQL Downloads for Windows]: https://dev.mysql.com/downloads/installer/
[Accessing data with MySQL]: https://spring.io/guides/gs/accessing-data-mysql/
