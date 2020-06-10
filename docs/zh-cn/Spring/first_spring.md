# Hello Spring
## 引入新的maven plugin
- lombok 注解相关
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

# Spring单数据源配置
## 引入新的maven plugin
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
- 新注解
	- @RequestMapping 注解路由
	- @RestController Spring的RestController注解