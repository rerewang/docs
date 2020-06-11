# Hello Spring
## 引入新的maven plugin
- lombok 注解 ？
```java
package com.rere.learn;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class LearnApplication {

    public static void main(String[] args) {
        SpringApplication.run(LearnApplication.class, args);
    }

    @RequestMapping("/hello")
    public String hello() {
        return "Hello Spring";
    }
}
```
学习知识点：
- @RequestMapping 注解路由
- @RestController Spring的RestController注解
- 什么是Bean?

# 数据源
## 单数据源配置
### 引入新的maven plugin
- h2   java内置的简单数据库
- jdbc 用于执行sql语句的java API
```java
package com.rere.learn;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;


import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@SpringBootApplication
@Slf4j
public class DataSourceDemoApplication implements CommandLineRunner {
    @Autowired
    private DataSource dataSource;

    public static void main(String[] args) {
        SpringApplication.run(LearnApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        showConnection();
    }

    private void showConnection() throws SQLException {
        log.info("test");
        log.info(dataSource.toString());
        Connection conn = dataSource.getConnection();
        log.info(conn.toString());
        conn.close();
    }
}

```
学习知识点：
- @Slf4j 类的注解，日志API
- @Autowired 自动装载
- CommandLineRunner 接口，用于构建SpringBoot项目时预先数据的加载

## SpringBoot多数据源配置
@todo
### 好用的数据库连接池
@todo
#### HikariCP
- 快 

#### Alibaba Druid
- 监控

## 使用Spring JDBC访问数据库
### spring-jdbc
- core，JdbcTemplate等相关核心接口和类
- DataSource 数据源相关的辅助类
- object 将基本的JDBC操作封装成对象
- support 错误码等相关的辅助类

### 新注解
- @Component  通用注解 通用Bean
- @Resposity  数据仓库
- @Service
- @Controller

### JdbcTemplate
- query
- queryForObject
- queryForList
- update
- execute
- batchUpdate

```java

```