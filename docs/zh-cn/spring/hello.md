# Hello Spring
## Spring成员

### Spring Framework
- Spring MVC

### Spring Boot
- 快速构建应用
- 开箱即用，也可定制
- 非功能性特性(业界最佳实践)

### Spring Cloud
- 简化分布式系统开发的微服务框架


### 技术趋势
- java 8+ 、Kotlin  语言车轮滚滚向前
- WebFlux    		异步编程模式
- 去掉很多支持 		类库的社区活跃度、维护团队如何

#### Spring Boot 和 Spring Cloud 的出现是必然的的
- 开箱即用
- 与生态圈的深度整合
- 注重运维  
- Cloud Native的大方向
- 最佳实践固化到系统实现


## 准备
- java8+
- IDEA (lombok plugin)
- util包
- Slf4j
- lambda
- Maven
- sql语句
- web知识
- docker

## 引入新的maven plugin
- lombok 注解 @todo

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