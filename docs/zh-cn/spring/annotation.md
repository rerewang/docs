# 常用注解
Project Lombok 能够自动迁入IDE 和 构建工具

## Java Config
- @Configuration 配置类
- @ImportResource 注入xml等
- @ComponentScan  扫描哪些文件的bean
- @Bean
- @ConfigurationProperties 

## Bean 定义相关
- @Component / @Repository / @Service
- @Controller / @RestController
- @RequestMapping

## 注入相关
- @Autowired / @Qualifier / @Resource
- @Value

## 常用操作
- @Getter / @Setter
- @ToString
- @EqualsAndHashCode  覆盖默认的equals 和 hashCode
- 构造器
	- @NoArgsConstructor 	生成无参构造器
	- @RequiredArgsConstructor 		生成包含final和@NonNull注解的成员变量的构造器
	- @AllArgsConstructor		生成全参构造器
- @Data 是以下注解的集合 @ToString @Getter @Setter @EqualsAndHashCode @RequiredArgsConstructor
- @Builder  建造者模式
- @Slf4j / @CommonsLog / @Log4j2

## 条件注解
(spring 4)
- @Conditional
- 类条件
	- @ConditionalOnClass
	- @ConditionalOnMissingClass
- 属性条件
	- @ConditionalOnProperty
- Bean条件
	- @ConditionalOnBean
	- @ConditionalOnMissingBean
	- @ConditionalOnSingleCondidate
- 资源条件
	- @ConditionalOnResource
- Web应用条件
	- @ConditionalOnWebApplication
	- @ConditionalOnNotWebApplication
- 其他条件
	- @ConditionalOnExpression
	- @ConditionalOnJava
	- @ConditionalOnJndi
