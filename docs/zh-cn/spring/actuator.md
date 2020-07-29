# Actuator
## 概览
- 目的
	- 监控并管理应用程序
- 访问方式
	- HTTP
	- JMX
- 依赖
	- spring-boot-starter-actuator


### 常用的 Endpoint
|ID|说明|默认开启|默认HTTP|默认JMX|
|beans|显示容器中的 Bean 列表|Y|N|Y|
|caches|显示应用中的缓存|Y|N|Y|
|conditions|显示配置条件的计算情况|Y|N|Y|
|configprops|显示@ConfigurationProperties的信息|Y|N|Y|
|env|显示ConfigurableEnvironment中的属性|Y|N|Y|
|helath|显示健康检查信息|Y|Y|Y|
|httptrace|显示HTTP Trace信息|Y|N|Y|
|info|显示设置好的应用信息|Y|Y|Y|

|logger|显示并更新日志配置|Y|N|Y|
|metrics|显示应用的度量信息|Y|N|Y|
|mappings|显示所有的 @RequestMapping 信息|Y|N|Y|
|scheduledtasks|显示所有的任务调度信息|Y|N|Y|
|shutdown|优雅地关闭应用程序|N|N|Y|
|threaddump|执行 Thread Dump|Y|N|Y|
|heapdump|返回 HeapDump，格式为 HPROF|Y|N|N/A|
|prometheus|返回可被 Prometheus 抓取的信息(需要安装相关依赖)|Y|N|N/A|

## 如何访问 Endpoint
#### HTTP 访问
- /actuator/<id>

#### 端口与路径
- management.server.address=
- management.server.port=
- management.endpoints.web.base_path=/actuator
- management.endpoints.web.path-mapping.<id>=路径

#### 开启、关闭与暴露
- management.endpoints.<id>.enabled=true
- management.endpoints.<id>.enabled-by-default=false
- management.endpoints.web.exposure.include=helath, info
- management.endpoints.web.exposure.exclude=
- management.endpoints.jmx.exposure.include=*
- management.endpoints.jmx.exposure.exclude=

## Health Indicator
### Spring Boot 的 Health Indicator
- 目的
	- 检查应用程序的运行状态
- 状态
	- DOWN - 503
	- OUT_OF_SERVICE - 503
	- UP - 200
	- UNKOWN - 200
- 机制
	- 通过 HealthIndicatorRegistry 收集信息
	- HealthIndicator实现具体检查逻辑
- 配置项
	- management.health.default.enabled=true|false
	- management.health.<id>.enabled=true
	- management.endpoints.health.show-details=never|when-authorized|always

#### 内置 Health Indicator 清单
|Name	|Description|
|CassandraHealthIndicator	|Checks that a Cassandra database is up.|
|DiskSpaceHealthIndicator	|Checks for low disk space.|
|DataSourceHealthIndicator	|Checks that a connection to DataSource can be obtained.|
|ElasticsearchHealthIndicator	|Checks that an Elasticsearch cluster is up.|
|JmsHealthIndicator	|Checks that a JMS broker is up.|
|MailHealthIndicator	|Checks that a mail server is up.|
|MongoHealthIndicator	|Checks that a Mongo database is up.|
|RabbitHealthIndicator	|Checks that a Rabbit server is up.|
|RedisHealthIndicator	|Checks that a Redis server is up.|
|SolrHealthIndicator	|Checks that a Solr server is up.|

### 自定义 Health Indicator
- 实现 HealthIndicator 接口
- 根据自定义检查逻辑返回对应Health状态
	- Health中包含状态和详细描述信息

## Micrometer
### 认识 Micrometer
#### 特性
- 多维度度量
	- 支持 Tag
- 预置大量探针
	- 缓存、类加载器、GC、CPU利用率、线程池...
- 与 Spring 深度整合
- 支持多种监控系统
	- Dimensional(象限)
		- AppOptics, Atlas, Azure Monitor, Cloudwatch, Datadog, Datadog StatsD, Dynatrace, Elastic, Humio, Influx, KairosDB, New Relic, Prometheus, SignalFx, Sysdig StatsD, Telegraf StatsD, Wavefront
	- Hierarchical(分级)
		- Graphite, Ganglia, JMX, Etsy StatsD

#### 一些核心度量指标
- 核心接口
	- Meter
- 实现
	- Gauge, TimeGuage
	- Timer, LongTaskTimer, FunctionTimer
	- Counter, FunctionCounter
	- DistributionSummary (分布统计)

### Micrometer in Spring Boot 2.x
- 一些URL
	- /actuator/metrics
	- /actuator/prometheus
- 一些配置项
	- management.metrics.export.*
	- management.metrics.tag.*
	- management.metrics.enable.*
	- management.metrics.distribution.*
	- management.metrics.web.server.auto-time-requests (web耗时请求监控)
- 核心度量项
	- JVM、CPU、文件句柄数、日志、启动时间
- 其他度量项
	- Spring MVC、Spring WebFlux
	- Tomcat、Jersey JAX-RS
	- RestTemplate、WebClient
	- 缓存、数据源、Hibernate
	- Kafka、RabbitMQ

### 自定义度量指标
- 通过 MeterRegistry 注册 Meter
- 提供 MeterBinderBean 让 SpringBoot 自动绑定
- 通过 MeterFilter 进行定制

## Spring Boot Admin
### 概览
-目的
	- 为 Spring Boot 应用程序提供一套管理界面
- 主要功能
	- 集中展示应用程序 Actuator 相关的内容
	- 变更通知 

### 快速上手
- 服务端
	- de.codecentric:spring-boot-admin-starter-server:2.1.3
	- @EnableAdminServer
- 客户端
	- de.codecentric:spring-boot-admin-starter-client:2.1.3
	- 配置服务端及 Endpoint
		- spring.boot.admin.client.url=http://localhost:8080
		- management.endpoints.web.exposure.exclude=*

### 安全控制
- 相关依赖
	- spring-boot-starter-security
- 服务端配置
	- spring.security.user.name
	- spring.security.user.password
- 客户端配置
	- spring.boot.admin.client.user.name
	- spring.boot.admin.client.user.password
	- spring.boot.admin.client.instance.metadata.user.name
	- spring.boot.admin.client.instance.metadata.user.password
