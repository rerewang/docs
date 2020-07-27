# Spring Boot

## 是什么
### Spring Boot 不是什么
- 不是应用服务器
- 不是 Java EE 之类的规范
- 不是代码生成器
- 不是 Spring Framework 的升级版

### Spring Boot 特性
- 方便地创建可独立执行的 Spring 应用程序
- 直接内嵌 Tomcat、Jetty 或 Undertow
- 简化了项目的构建配置 (Maven / gradle)
- 为 Spring 及第三方库提供自动配置
- 提供生产机特性 (eg: 监控)
- 无需生成代码或进行 XML 配置

### Spring Boot 四大核心
- 自动配置 - Auto Configuration
- 起步依赖 - Starter Dependency
- 命令行界面 - Spring Boot CLI
- Actuator

## 自动配置
- 基于添加的 JAR 依赖自动对 Spring Boot 应用程序进行配置
- spring-boot-autoconfiguration

### 开启自动配置
- @EnableAutoConfiguration
	- exclude = class<?>[]
- @SpringBootApplication

### 实现原理
[自动配置原理](https://www.cnblogs.com/jiadp/p/9276826.html)
@EnableAutoConfiguration
- AutoConfigurationImportSelector
- MATE-INF/spring.factories
	- org.springframework.boot.autoconfiguration.EnableAutoConfiguration

### 了解自动配置的情况
观察自动配置的判断结果
- --debug (run 时 cli)
ConditionEvaluationReportLoggingListener
- Positvie matches (匹配到的)
- Negative matches (没匹配到的)
- Exclusions (排除)
- Unconditional classes (无条件配置)

### 动手实现自己的自动配置
#### 主要工作
- 编写 Java Config
	- @Configuration
- 添加条件
	- @Conditional
- 定位自动配置
	- MATE-INF/spring.factories

#### 执行顺序
- @AutoConfigurationBefore
- @AutoConfigurationAfter
- @AutoConfigurationOrder

#### 在低版本下实现自动配置
@todo

## 起步依赖

## Actuator

## Spring Boot CLI

## 部署

