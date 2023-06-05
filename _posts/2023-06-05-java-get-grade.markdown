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

> 1 和 2 参考了 Spring Boot 的这篇 [Accessing data with MySQL]，缝合了 [Error Handling for REST with Spring] ；3 和 4 是作者作为缝合怪补充的内容

原版使用的场景是保存用户的姓名和邮箱，这里改编成了在数据库里保存学生的姓名和成绩

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

本机运行可以省略 host，这里我们以 root 身份登录，输入安装时填写的密码：

```shell
mysql -u root -p
Enter password: ******
```

想要退出的时候输入 quit 回车即可

```shell
mysql> quit
```

下面我们以 root 身份新建一个数据库和一个用户，使这个用户拥有该数据库的权限

这里假设我们的数据库是 “学校”，我们想要在数据库里存学生的 id，姓名，和成绩

创建的用户叫 julia，登录密码是 123456

```shell
mysql> create database school;
mysql> create user 'julia'@'%' identified by '123456';
mysql> grant all on school.* to 'julia'@'%';
```

1. 命令需要以分号结尾
2. "@" 后面跟的是 hostname，省略的时候相当于 "%"，表示该用户可以从任意主机连接服务器
3. 第一步创建数据库，第二部创建用户，第三步给予权限

### Spring Boot
使用 [Spring Initializr] 下载初始化项目文件的压缩包

![initializr](/assets/get-grade/initializr.png)

新建 `src/main/resouces/application.properties` 文件，相应修改数据库名称、用户名，和用户密码

这里告诉了对面我们要连接哪个数据库，并把我们拥有的登录信息给到了对面

```java
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/school
spring.datasource.username=julia
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#spring.jpa.show-sql: true
```
新建文件 `src/main/java/com/example/accessingdatamysql/Student.java`

`@Entity` 表示会使用这个 class 建一个 table

这里告诉了对面我们要建一个名叫 student 的表，有这几个列：id，name，grade，其中 id 会自动生成：

```java
package com.example.accessingdatamysql;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Student {
    @Id
    @GeneratedValue(strategy= GenerationType.AUTO)
    private Integer id;

    private String name;

    private Integer grade;

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
       return this.name;
    }

    public void setGrade(Integer grade) {
        this.grade = grade;
    }

    public Integer getGrade() {
        return this.grade;
    }
}
```
新建文件 `src/main/java/com/example/accessingdatamysql/StudentRepository.java`

这一步让我们可以用实例去处理一些增删改查的逻辑

```java
package com.example.accessingdatamysql;

import org.springframework.data.repository.CrudRepository;

public interface StudentRepository extends CrudRepository<Student, Integer> {

}
```

接下来开始写接口，这里省略了 import 的部分，可以用 IDE 的 auto import

这里定义了两个 POST 接口，创建的时候传参是 { name: "Julia", grade: 100 }，会返回一个字符串；读取的时候传参是 { id: 1 }，成功找到 id 会返回一个 Student Object，失败时会抛出一个自定义的错误实例（见下文）

```java
// MainController.java
@Controller
@RequestMapping(path="/students")
public class MainController {
    @Autowired
    private StudentRepository studentRepository;

    @PostMapping(path="/add")
    public @ResponseBody String addStudent(@RequestBody Student student) {
        studentRepository.save(student);
        return String.format("Student %s added", student.getName());
    }

    @PostMapping(path="/grade")
    public ResponseEntity<Object> getGrade(@RequestBody Input input) {
        Optional<Student> studentOptional = studentRepository.findById(input.id());
        if (studentOptional.isEmpty()) throw new StudentNotFoundException();
        return new ResponseEntity<>(studentOptional.get(), HttpStatus.OK);
    }
}
```

```java
// Input.java
package com.example.accessingdatamysql;

public record Input(int id) {}
```


### Postman
TBC

### Swagger
TBC

### Reference
[MySQL Documentation]

[Accessing data with MySQL]

[Error Handling for REST with Spring]

[MySQL Documentation]: https://dev.mysql.com/doc/
[MySQL Downloads for Windows]: https://dev.mysql.com/downloads/installer/
[Error Handling for REST with Spring]: https://www.baeldung.com/exception-handling-for-rest-with-spring
[Spring Initializr]: https://start.spring.io/#!type=maven-project&language=java&platformVersion=3.1.0&packaging=jar&jvmVersion=17&groupId=com.example&artifactId=accessing-data-mysql&name=accessing-data-mysql&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.accessing-data-mysql&dependencies=web,data-jpa,mysql
[Accessing data with MySQL]: https://spring.io/guides/gs/accessing-data-mysql/
