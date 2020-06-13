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
        SpringApplication.run(DataSourceDemoApplication.class, args);
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
- lombok
	- @Data  set方法
	- @Builder 构造方法

### JdbcTemplate
- query
- queryForObject
- queryForList
- update
- execute
- batchUpdate
- simpleJdbcInsert
	- executeAndReturnKey  执行并返回主键

### RowMapper接口
**jdbc从数据库查询出来的记录保存在ResultSet中，需要将结果一条条获取并设置到实体类中，RowMapper就是方便做这些事情的**
- SingleColumnRowMapper 适用于单列数据
- BeanPropertyRowMapper 适用于多列数据映射到某个具体的实体类上
- 自定义RowMapper  重写mapRow方法，eg:FooDao.list()中的fooList

### 代码
#### Foo
```java
package com.rere.learn.jdbcDemo;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class Foo {
    private Long id;
    private String bar;
}
```
#### FooDao
```java
package com.rere.learn.jdbcDemo;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
import org.springframework.stereotype.Repository;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.List;

@Slf4j
@Repository
public class FooDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Autowired
    private SimpleJdbcInsert simpleJdbcInsert;

    public void createTable() {
        jdbcTemplate.execute("create table foo (\n" +
                "    \"id\" integer auto_increment,\n" +
                "    \"bar\" varchar(20)\n" +
                ")");
    }

    public void insert() {
//        String bar = "bar1";
//        jdbcTemplate.update("insert into foo (bar) value (?)", bar);

        HashMap<String, String> row = new HashMap<>();
        row.put("bar", "bar2");
        Number id = simpleJdbcInsert.executeAndReturnKey(row);
        log.info("ID of bar2: {}", id.longValue());
    }

    public void list() {
        //count
        log.info("Count is {}",
            jdbcTemplate.queryForObject("select count(1) from foo", Long.class)
        );

        //list
        List<String> list = jdbcTemplate.queryForList("select bar from foo", String.class);
        list.forEach(s -> log.info("Bar: {}", s));

        //list object
        List<Foo> fooList = jdbcTemplate.query("select * from foo", new RowMapper<Foo>() {
        	//自定义RowMapper
            @Override
            public Foo mapRow(ResultSet rs, int rowNum) throws SQLException{
                return Foo.builder()
                        .id(rs.getLong("id"))
                        .bar(rs.getString("bar"))
                        .build();
            }
        });
        fooList.forEach(foo -> log.info("Foo is {}", foo));
    }
}
```
#### JdbcDemoApplication
```java
package com.rere.learn.jdbcDemo;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@SpringBootApplication
@RestController
@Slf4j
public class JdbcDemoApplication {
    @Autowired
    private FooDao fooDao;

    @Autowired
    private DataSource dataSource;

    public static void main(String[] args) {
        SpringApplication.run(JdbcDemoApplication.class, args);
    }

    @Bean
    @Autowired
    public SimpleJdbcInsert simpleJdbcInsert(JdbcTemplate jdbcTemplate) {
        return new SimpleJdbcInsert(jdbcTemplate)
                .withTableName("foo").usingGeneratedKeyColumns("id");
    }

    @RequestMapping("/insert")
    public String insert() {
        fooDao.insert();
        return "insert";
    }

    @RequestMapping("/list")
    public String list() {
        fooDao.list();
        return "list";
    }

    @RequestMapping("/source")
    public String source() throws SQLException {
        Connection conn = dataSource.getConnection();
        return conn.toString();
    }

    @RequestMapping("/create_table")
    public String create() {
        fooDao.createTable();
        return "created";
    }
}
```