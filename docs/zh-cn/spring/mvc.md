# Spring MVC

## 认识 Spring MVC
### DispatcherServlet
- Controller
- xxxResolver
	- ViewResolver
	- HandlerExceptionResolver
	- MultipartResolver
- HandingMapping

### 常用注解
- @Controller
	- @RestController
- @RequestMapping
	-@GetMapping / @PostMapping
	-@PutMapping / @DeleteMapping
- @RequestBody / @ResponseBody / @ResponseStatus

## ApplicationContext 应用程序上下文
### 相关接口与实现
- BeanFactory
	- DefalutListableBeanFactory
- ApplicationContext
	- ClassPathXmlApplicationContext
	- FileSystemXmlApplicationContext
	- AnnotationConfigApplicationContext
- WebApplicationContext

### 上下文层次


## 请求处理机制

### 一个请求的大致处理流程
1. 绑定一些Attribute
	- WebApplicationContext / LocalResolver / ThemeResolver
2. 处理 Multipart
	- 如果是，则将请求转成 MultipartHttpServletRequest
3. Handler处理
	- 如果找到对应的Handler， 执行Controller及前后置处理器逻辑
4. 处理返回的model，呈现视图

## Controller
### 定义映射关系
- @Controller
- @RequestMapping
	- patch / method 指定映射路径与方法
	- params / header 限定映射范围
	- consumes / produces 限定请求与响应格式
- 一些快捷方式
	- @RestController
	-@GetMapping / @PostMapping / @PutMapping / @DeleteMapping / @PatchMapping

### 定义处理方法
- @RequestBody / @ResponseBody / @ResponseStatus
- @PathVariable / @RequestParam / @RequestHeader
- HttpEntity / ResponseEntity
[详细参数](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-arguments)
[详细返回](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-return-types)

### 定义类型转换
- Web

### 定义校验
- 通过 Validator 对绑定结果进行校验
	- Hibernate Validator
- @Valid 注解
- BingResult

### Mutlipart上传
- 配置MutlipartResolver
	- Spring Boot自动配置 MutlipartAutoConfigration
- 支持 mutlipart/form-data
- MutlipartFile 类型


## 视图
### 视图解析的实现基础
ViewResolver 与 View 接口
- AbstractCachingViewResolver
- UrlBasedViewResolver
- FreeMarkerViewResolver
- ContentNegotiatingViewResolver
- InternalResourceViewResolver

### DispatcherServlet中的视图解析逻辑
- initStrategies()
	- initViewResolvers() 初始化对应的ViewResolver
- doDispatch()
	- processDispatchResult()
		- 没有返回视图的话，尝试 RequestToViewNameTranslator
		- resolveViewName() 解析View对象

### 使用 @ResponseBody 的情况
- 在HandlerAdapter.handle()中完成Response输出
	- RequestMappingHandlerAdapter.invokeHandlerMethod()
		- 

## 静态资源与缓存
### spring boot中的静态资源配置
- 核心逻辑 
	- WebMvcConfigurer.addResourceHandlers()
- 常用配置
	- spring.mvc.static-path-pattern=/**
	- spring.resources.static-locations

### spring boot中的缓存配置
- 常用配置
	- ResourceProperties.Cache 类
	- spring.resources.cache.cachecontrol.max-age=时间
	- spring.resources.cache.cachecontrol.no-cache=true/false
	- spring.resources.cache.cachecontrol.s-max-age=时间

## 异常处理机制
- 核心接口
	- ExceptionHandlerExceptionResolver
- 实现类
	- SimpleMappingExceptionResolver
	- ResponseStatusExceptionResolver   @ResponseStatus
	- DefaultHandlerExceptionResolver
	- ExceptionHandlerExceptionResolver

## 异常处理方法
- @ExceptionHandler  (标明是用来处理异常的)
- 添加位置
	- @Controller / @RestController
	- @ControllerAdvice / @RestControllerAdvice   //优先级低

## Spring MVC的切入点
### Spring MVC的拦截器
- 核心接口
	- HandlerInterceptor
		- preHandle()
		- postHandle()
		- afterCompletion()
- 针对 @ResponseBody 和 @ResponseEntity
	- ResponseBodyAdvice
- 针对异步请求的接口
	- AsyncHandlerInterceptor
		- void afterConcurrentHandlingStarted()

### 拦截器的配置方式
- 常规方法
	- WebMvcConfigurer.addInterceptors()
- Spring Boot中的配置
	- 创建一个 带有 @Configuration 的 WebMvcConfigurer配置类
	- 不能带 @EnableWebMvc (想彻底做自己控制MVC配置除外)

## RestTemplate
- Spring Boot没有提供 RestTemplate 的自动配置
- Spring Boot提供了 RestTemplateBuilder
	- RestTemplateBuilder.build()

### 常用方法
- getForObject() / getForEntity()
- postForObject() / postForEntity()
- put()
- delete()

### 构造URI
- 构造URI
	- UriComponentsBuilder
- 构造相对于当前请求的URI
	- ServletUriComponentsBuilder
- 构造指向Controller的URI
	- MvcUriComponentsBuilder

### 高阶用法
#### 传递 HTTP Header
- RestTemplate.exchange()
- RequestEntity<T> / ResponseEntity<T>

#### 类型转换
- JsonSerializer / JsonDeserializer
- @JsonComponent

#### 解析泛型对象
- RestTemplate.exchange()
- ParameterizedTypeReference<T>  可以用来定义泛型

### 简单定制 RestTemplate
#### 支持的HTTP库
- 通用接口
	- ClientHttpRequestFactory
- 默认实现
	- SimpleClientHttpRequestFactory
- Apache HttpComponents
	- HttpComponentsClientHttpRequestFactory
- Netty
	- Netty4ClientHttpRequestFactory
- OkHttp
	- OkHttp3ClientHttpRequestFactory  //安卓常用

#### 优化底层请求策略
- 连接管理
	- PoolingHttpClientConnectionManager
	- KeepAlive 策略
- 超时设置
	- connectTimeout / readTimeout
- SSL证书校验
	- 证书校验策略

#### 更多，访问Apache HttpComponents 官网

## WebClient
WebClient 是一个以 Reactive 方式处理 HTTP 请求的非阻塞式的客户端

### 支持的HTTP库
- Reactor Netty - ReactorClientHttpConnector  //用的更多
- Jetty ReactiveStreamHttpClient - JettyClientHttpConnector

### 基本用法
- 创建 WebClient
	- WebClient.create()
	- WebClient.builder()
- 发起请求
	- get() / post() / put() / delete() / patch()
- 获取结果
	- retrieve() / exchange()
- 处理 HTTP Status
	- onStatus()
- 应答正文
	- bodytoMono()
	- bodytoFlux()

