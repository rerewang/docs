# Spring Clound
## 认识
### 主要功能
- 服务发现
- 服务熔断
- 配置服务
- 服务安全
- 服务网关
- 分布式消息
- 分布式跟踪
- 各种云平台支持

### Spring Clound 的版本号规则
- Spring Clound 是个大工程，其中包含多个独立项目
- Bom - Release Train
	 - London Tube Stations
	 - 字母序排列
	 	- Greenwich, Finchley, Edgeware...
	 - SR - Service Release

## 服务注册中心
### Eureka
- 什么是 Eureka
	- Eureka 是 Aws 上定位服务的 REST 服务
- Netflix OSS
	- https://netflix.github.io
- Spring 对 Netflix 套件的支持
	- Spring Cloud Netflix

#### 在本地起一个 Eureka 服务
- Starter 
	- spring-cloud-dependencies
	- spring-cloud-starter-netflix-eureka-starter
- 声明
	- @EnableEurekaServer
- 注意事项
	- 默认端口 8761
	- Eureka 自己不要注册到 Eureka 了

#### 将服务注册到 Eureka Server
- Starter
	- spring-cloud-starter-netflix-eureka-client
- 声明
	- @EnableDiscoveryClient
	- @EnableEurekaClient
- 一些配置项
	- eureka.client.service-url.default-zone
	- eureka.client.instance.prefer-ip-address

#### 关于 Bootstrap 属性
Bootstrap 属性
- 启动引导阶段加载的属性
- bootstrap.properties|.yml
- spring.cloud.bootstrap.name=bootstrap
常用配置
- spring.application.name=应用名
- 配置中心相关

### Spring Cloud Load Balancer
#### 如何获取服务地址
- EurekaClient
	- getNextServerFromEureka
- DiscoveryClient
	- getInstances

### Load Balancer Client
RestTemplate 与 WebClient
- @LoadBalanced
- 实际是通过 ClientHttpRequestInterceptor 实现的
	- LoadBalancerInterceptor
	- LoadBalancerClient
		- RibbonLoadBalancerClient

## 服务熔断与限流


