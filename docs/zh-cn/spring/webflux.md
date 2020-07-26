# WebFlux

## 认识 WebFlux
### 什么是 WebFlux
- 用于构建基于 Reactive 技术栈上的 Web 应用程序
- 基于 Reative Streams API, 运行在非阻塞服务器上

### 为什么需要 WebFlux
- 对于非阻塞 Web 应用的需要
- 函数式编程

### 关于 WebFlux 的性能
- 单次请求的耗时并不会有很大的改善
- 仅需少量固定数量的现成和较少的内存即可实现扩展

### WebMVC vs WebFlux
- 已有 Springb MVC 应用，且运行正常，就别改了
- 依赖了大量阻塞式持久化 API 和网络 API (eg: mysql)，建议使用 Spring MVC
- 已经使用了非阻塞技术栈，可以考虑使用 WebFlux
- 想要使用 Java8 Lambda 结合轻量级函数式框架，可以考虑 WebFlux 框架

### WebFlux 的编程模型
- 基于注解的控制器
- 函数式 Endpoints

#### 基于基于注解的控制器
常用注解
- @Controller
- @RequestMapping 及其等价注解
- @RequestBody / @ResponseBody

返回值
- Mono<T> / Flux<T>



