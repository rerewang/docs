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





