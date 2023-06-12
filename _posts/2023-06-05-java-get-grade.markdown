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

> 1 和 2 参考了 Spring Boot 的这篇 [Accessing data with MySQL]，缝合了 [Error Handling for REST with Spring]；3 和 4 是作者作为缝合怪补充的内容

原版使用的场景是保存用户的姓名和邮箱，这里改编成了在数据库里保存学生的姓名和成绩

👉 [Source Code]

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

这一步用来指明要连接哪个数据库，对应的登录信息是什么

```java
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/school
spring.datasource.username=julia
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#spring.jpa.show-sql: true
```
新建 `src/main/java/com/example/accessingdatamysql/Student.java` 文件

`@Entity` 标注了这个类会用来建表

这一步定义了表的结构，有 id、name、grade 这几列，其中 id 会自动生成

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
新建 `StudentRepository.java`

这一步让我们可以用实例去处理一些增删改查的逻辑

```java
package com.example.accessingdatamysql;

import org.springframework.data.repository.CrudRepository;

public interface StudentRepository extends CrudRepository<Student, Integer> {
}
```

新建 `MainController.java`

这里定义了两个 POST 接口，输入和输出都是 JSON

```java
package com.example.accessingdatamysql;

import com.example.accessingdatamysql.exception.StudentNotFoundException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.Optional;

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
第一个接口的 request 类是之前定义过的 Student，第二个接口的 request 类定义如下

```java
package com.example.accessingdatamysql;

public record Input(int id) {}
```

> 资深 Java 工程师这样说：一般一个接口就定义一个 request 类、一个 response 类，代码即协议，在上面还能写注释

第二个接口里，当输入的 id 在数据库里找不到时，抛出了一个异常类的实例，下面我们来详细看看这个异常类是怎么定义的

> 资深 Java 工程师这样说：一般业务异常可以都放在 response 里，给出 errorCode 和 errorMsg，然后 http status 还是 200；返回给前端的时候，一般有一步是把异常统一转化为统一的 response，有多种实现方式，介绍几种，关键词 aop，@controlleradvice，@exceptionhandler

在 `src/main/java/com/example/accessingdatamysql/exception` 下新建 `SchoolExceptionController.java` 文件

`@ControllerAdvice` 代表这里是全局异常处理的入口

`@ExceptionHandler` 后面定义了每一种类型异常的处理方法

```java
package com.example.accessingdatamysql.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class SchoolExceptionController {
    @ExceptionHandler(value = StudentNotFoundException.class)
    public ResponseEntity<Object> exception(StudentNotFoundException exception) {
        return new ResponseEntity<>(new Error(12345, "This student does not exist. "), HttpStatus.OK);
    }
}

```
```java
package com.example.accessingdatamysql.exception;

public record Error(Integer errorCode, String errorMsg) {}
```

```java
package com.example.accessingdatamysql.exception;

public class StudentNotFoundException extends RuntimeException {
}
```
结合起来看，当我们抛出一个异常：

```java
throw new StudentNotFoundException();
```

`SchoolExceptionController` 会接到这个异常

因为这个异常是 `StudentFoundException` 类的一个实例，会调用 `exception` 方法进行处理

最终会返回一个状态码为 200，body 为 `{ errorCode: 12345, errorMsg: "This student does not exist. " }` 的 response

接下来，我们可以用 `./gradlew bootRun` 或 `./mvnw spring-boot:run` 让程序跑起来

```shell
curl -X POST http://localhost:8080/students/add -H 'Content-Type: application/json' -d '{ "name": "Julia", "grade": 100 }'
```

添加成功后应该可以看到 `Student Rpuck added`

类似地，可以用 cURL 命令获取学生的成绩

```shell
curl -X POST http://localhost:8080/students/grade -H 'Content-Type: application/json' -d '{ "id": 1 }'
```

得到 `{"name":"Julia","grade":100}`

最后，通过 MySQL 命令：

查看所有数据库

```shell
mysql> show databases;
```
选中其中一个数据库，以我们之前建的 `school` 为例

```shell
mysql> use school;
```

查看该数据库里的所有表

```shell
mysql> show tables;
```

查看表里所有记录

```shell
mysql> select * from student;
```

### Postman
一个很好用的接口测试工具，虽然使用 cURL 命令也可以达到同样的目的，不过 Postman 在用户体验上做得还是非常不错的，提供了一个简单易用的 UI 界面

![postman](/assets/get-grade/postman.png)

[Postman Downloads]

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
[Source Code]: https://github.com/zzhiyuan98/access-data-mysql
[Postman Downloads]: https://www.postman.com/downloads/
