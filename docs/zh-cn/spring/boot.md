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

## Spring Boot 外部化配置
### 外部化配置加载顺序
- 开启DevTools时，~/.spring-boot-devtools.properties
- 测试用例上的@TestPropertySource注解。
- 测试用例上的@SpringBootTest#properties注解。
- 命令行参数 【常用】
- 来自SPRING_APPLICATION_JSON的属性（环境变量或系统属性中内嵌的内联JSON）
- ServletConfig初始化参数
- ServletContext初始化参数
- 来自于java:comp/env的JNDI属性
- Java系统属性（System.getProperties()）【常用】
- 操作系统环境变量【常用】
- RandomValuePropertySource，只包含random.*中的属性
- 没有打进jar包的Profile-specific应用属性（application-{profile}.properties和YAML变量）
- 打进jar包中的Profile-specific应用属性（application-{profile}.properties和YAML变量）
- 没有打进jar包的应用配置（application.properties和YAML变量）
- 打进jar包中的应用配置（application.properties和YAML变量）
- @Configuration类上的@PropertySource注解
- 默认属性（使用SpringApplication.setDefaultProperties指定）

### 配置文件所在位置
#### 默认位置
- ./config
- ./
- CLASSPATH 中的 /config
- CLASSPATH 中的 /

#### 修改名字或路径
- spring.config.name
- spring.config.location
- spring.config.additional-location

### PropertySource
#### 添加 PropertySource
- <context:property-placeholder>
- PropertySourcePlaceholderConfigurer
	- - PropertyPlaceholderConfigurer
- @PropertySource
- @PropertySources
- Spring Boot 中的 @ConfigurationPorperties
	- 可以将属性绑定到结构化对象上
	- 支持 Relaxed Binding
	- 支持安全的类型转换
	- @EnableConfigurationPorperties

#### 定制 PropertySource
可以用来做自己的配置中心
##### 主要步骤
- 实现 PropertySource<T>
- 从 Environment 取得 PropertySource
- 将自己的 PropertySource 添加到合适的位置

##### 切入位置
- EnvironmentPostProcessor
- BeanFactoryPostProcessor

#### Relaxed Binding
|命名风格|使用范围|示例|
|短划线分隔|Properties文件/Yaml文件/系统属性|geektime.spring-boot.first-demo|
|驼峰分隔|同上|geektime.springBoot.firstDemo|
|下划线分隔|同上|geektime.spring_boot.first_demo|
|全大写，下划线分隔|环境变量|GEEKTIME_SPRINGBOOT_FIRSTDEMO|


## 起步依赖
### 关于 Maven 依赖管理的一些小技巧
- 了解你的依赖
	- mvn dependency:tree
	- IDEA Maven Helper 插件
- 排除特定依赖
	- exclusion
- 统一管理依赖
	- dependencyMangement
	- Bill of Materials - bom

官方Starters
- spring-boot-starter-*

### Starter Dependencies
- 直接面向功能
- 一站获得所以相关依赖，补在复制粘贴

### 动手实现自己的起步依赖
#### 主要内容
- autoconfigure模块，包含自动配置代码
- starter模块，包含指向自动配置模块的依赖及其他相关依赖

#### 命名方式
- xxx-spring-boot-autoconfigure
- xxx-spring-boot-starter

#### 一些注意事项
- 不要使用 spring-boot 作为依赖的前缀
- 不要使用 spring-boot 的配置命名空间
- starter 中仅添加必要的依赖
- 声明对 spring-boot-starter 的依赖

## Actuator
[Actuator](zh-cn/spring/actuator)

## Web 容器
### 内置 Web 容器
- 可选容器列表
	- spring-boot-starter-tomcat
	- spring-boot-starter-jetty
	- spring-boot-starter-undertow
	- spring-boot-starter-reactor-netty
- 配置
	- 端口
		- server.port
		- server.address
	- 压缩
		- server.compression.enabled
		- server.compression.min-response-size
		- server.compression.mine-types
	- Tomcat 特定配置
		- server.tomcat.max-connections=10000
		- server.tomcat.max-http-post-size=2MB
		- server.tomcat.max-swallow-size=2MB
		- server.tomcat.max-threads=200
		- server.tomcat.max-spare-threads=10
	- 错误处理
		- server.error.path=/error
		- server.error.include-exception=false
		- server.error.include-stacktrace=nerver
		- server.error.whitelabel.enable=true
	- 其它
		- server.use-forward-headers
		- server.servlet.session.timeout

### 编程方式
- WebServerFactoryCustomizer<T>
	- TomcatServletWebServerFactory
	- JettyServletWebServerFactory
	- UndertowServletWebServerFactory

### 配置 Web 容器支持 HTTPS
#### 配置服务端支持HTTPS 
- 通过参数进行配置
	- server.port=8443
	- server.ssl.*
		- server.ssl.key-store
		- server.ssl.key-type, JKS或PKCS12
		- server.ssl.key-password=secret
- 生成证书文件
	- 命令
		- keytool -genkey -alias 别名 -storetype仓库类型 -keyalg 算法 -keysize 长度 -keystore 文件名 -validity 有效期
	- 说明
		- 仓库类型，JKS、JCEKS、PKCS12 等
		- 算法类型，RSA、DSA 等
		- 长度，例如2048

#### 配置客户端支持 HTTPS
- 配置 HttpClient (>=4.4)
	- SSLContextBuilder 构造 SSLContext
	- setSSLHostnameVerifier(new NoopHostnameVerifier())
- 配置 RequestFactory
	- HttpComponentsClientHttpRequestFactory
		- setHttpClient

#### 配置支持 HTTP/2
- 前提条件
	- Java >= JDK 9
	- Tomcat >= 9.0.0
	- Sping Boot 不支持 h2c，需先配置 SSL
- 配置项
	server.http2.enabled
- HTTP 库选择
	- OkHttp (com.squareup.okhttp3:okhttp:3.14.0)
		- OkHttpClient
	- RestTemplate
		- OkHttp3ClientHttpRequestFactory

## Spring Boot CLI
### 关闭 Web 容器
- 控制依赖
	- 不添加 Web 相关依赖
- 配置方式
	- spring-main-web-application-type=none
- 编程方式
	- SpringApplication
		- setWebApplicationType
	- SpringApplicationBuilder
		- web()
	- 在调用 SpringApplication 的 run() 方法前设置 WebApplicationType

### 常用工具类
- 不同的 Runner
	- ApplicationRunner
		- 参数是 ApplicationArguments
	- CommandLineRunner
		- 参数是 String[]
- 多个 Runner 执行顺序
	- @order(1)
- 返回码 
	- ExitCodeGenerator
		- ExitCodeEvent

## 可执行Jar
### 认识
- 其中包含
	- Jar 描述，META-INF / MANIFEST.MF
	- Spring Boot Loader, org/springframework/boot/loader
	- 项目内容, BOOT-INF/class
	- 项目依赖, BOOT-INF/lib
- 其中不包含
	- JDK / JRE

### 程序的入口
- Jar 的启动类
	- MANIFEST.MF
		- Main-Class: org.springframework.boot.loader.jarLauncher
- 项目的主类
	- @SpringApplication
	- MANIFEST.MF
		- Start-Class: xxx.yyy.zzz

### 如何创建可直接执行的Jar
```JAVA
<plugin>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-maven-plugin</artifactId>
	<configuration>
		<executable>true</executable>
	</configuration>
</plugin>
```
- 打包后的 Jar 可直接运行， 无需 java 命令
- 可以在 .conf 中的同名文件中配置参数

#### 默认脚本中的一些配置项
|配置项|说明|备注|
|CONBFIG_FOLDER|放置 .conf 的目录位置|只能放在环境变量中|
|JAVA_OPTS|JVM启动时的参数|比如JVM的内存、GC|
|RUN_ARGS|传给程序执行的参数||

## 部署
### 将 SpringBoot 应用打包成 dokcer 镜像
[docker](zh-cn/ops/docker.md)