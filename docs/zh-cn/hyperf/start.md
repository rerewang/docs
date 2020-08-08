# 学习 hyperf

## 官方文档
https://hyperf.wiki/2.0/#/zh-cn/quick-start/overview

## 安装运行
- [使用docker运行hyperf](https://hyperf.wiki/2.0/#/zh-cn/quick-start/install?id=docker-%e4%b8%8b%e5%bc%80%e5%8f%91)
- 验证运行
```BASH
 curl 127.0.0.1:9501
 ```

## 路由
### 配置文件
- 与laravel类似
	- Router::get
	- Router::post
	- Router::addRoute

### 注解
- 与SpringBoot类似
- 使用时需要use相应的命名空间
- @AutoController
- @Controller
	- @RequestMapping
		- path
		- method
	- @GetMapping
	- @PostMapping
	- @PutMapping
	- @PatchMapping
	- @DeleteMapping

## 注解
- PHP 语法上没有支持注解，只能基于约束好的规定去注释上解析
- 注解只是元数据定义，单独使用时没有任何作用，需配合应用程序对其元数据进行利用才有作用

### 注解实现的原理
- 如何被扫描
- 如何被收集
@todo

### 实现自定义注解
@todo

## DI
- [DI](/zh-cn/dao/di.md)
- [hyperf 的 DI](https://hyperf.wiki/2.0/#/zh-cn/di)
	- 由 hyperf/di 组件提供功能支持
		- 更符合长生命周期的应用使用
		- 提供了 注解、注解注入、AOP
		- 基于 PSR-11 实现，可独立应用于其他框架
		- DI 管理的对象是单例
	- 注入方式
		- 通过构造方法注入
			- 在构造函数的参数上声明参数类型 Typehint
		- 通过 @Inject 注解注入
			- 在类成员属性上定义 @Inject 注解
			- 配合 @var 注解声明属性类型
	- 注入类型
		- 简单对象注入
		- 抽象对象注入
			- 通过 config/autoload/dependencies.php 来绑定对象关系
		- 工厂对象注入
			- 通过工厂类创建复杂的对象
			- 定义一个工厂类，于 __invoke() 方法内完成对象的生产
			- make() 函数创建短生命周期对象
		-懒加载
			- config/autoload/lazy_loader.php 文件并绑定懒加载关系
			- 或 @Inject(lazy=true) 注解
			- 懒加载对象执行下列操作时，才会真正被实例化
				- $proxy->someMethod();	// 方法调用
				- echo $proxy->someProperty;	// 读取属性
				- $proxy->someProperty = 'foo';	// 写入属性
				- isset($proxy->someProperty);	// 检查属性是否存在
				- unset($proxy->someProperty);	// 删除属性
* 注意 *
- 容器仅管理长生命周期的对象
	- 容器内管理的对象都是单例，均不能包含 状态 值
- 短生命周期对象
	- new 关键词创建的对象是短生命周期的
	- 创建一个短生命周期的对象但又希望使用 构造函数依赖自动注入功能： 使用 make() 函数
- 获取容器对象
	- 通过在 构造函数(Constructor) 声明或通过 @Inject 注解注入 Psr\Container\ContainerInterface 接口类都能够获得 Hyperf\Di\Container 容器对象
	- \Hyperf\Utils\ApplicationContext::getContaienr()

## AOP
- 切入的类必须由 DI 管理
- hyperf 里的 AOP 是基于 DI 实现的 (hyperf/di)
- 必须通过 DI 创建的对象才能是 AOP 生效，直接new不行
- 必须当代理类缓存文件不存在时才会重新生成代理类

## 协程

## 配置

## 中间件

## jsonRPC