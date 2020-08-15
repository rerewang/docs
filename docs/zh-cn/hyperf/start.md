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
- [注解](https://hyperf.wiki/2.0/#/zh-cn/annotation)
- PHP 语法上没有支持注解，只能基于约束好的规定去注释上解析
- 注解只是元数据定义，单独使用时没有任何作用，需配合应用程序对其元数据进行利用才有作用

### 注解实现的原理
- 如何被扫描
- 如何被收集
@todo

### 实现自定义注解
```PHP
<?php
namespace App\Annotation;

use Hyperf\Di\Annotation\AbstractAnnotation;

/**
 * @Annotation()
 * @Target({"CLASS", "METHOD"})
 * Class Foo
 * @package App\Annotation
 * 注意：
 *	1. 定义注解类时需要添加以上两个注解
 *	2. 以上两个注解是全局的，无需 use 命名空间
 *	3. Target 用来定义使用范围，注意只可以使用双引号
 *  4. 继承 AbstractAnnotation 即可
 */
class Foo extends AbstractAnnotation
{
    public $bar = 'bar';
}
```

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
- Aspect 可以切入任意类和注解类
- 切入的类必须由 DI 管理
- hyperf 里的 AOP 是基于 DI 实现的 (hyperf/di)
- 必须通过 DI 创建的对象才能是 AOP 生效，直接new不行
- 必须当代理类缓存文件不存在时才会重新生成代理类
- 每个 切面(Aspect) 必须定义 @Aspect 注解或在 config/autoload/aspects.php 内配置均可发挥作用。

### 基于 AOP 的功能
- @Cacheable、@CachePut、@CacheEvict
- @Task
- @RateLimit
- @CircuitBreaker
- 调用链追踪的无侵入打点

### 示例
#### classes 形式
```PHP
<?php
namespace App\Aspect;

use App\Controller\IndexController;
use Hyperf\Di\Annotation\Aspect;
use Hyperf\Di\Aop\AbstractAspect;
use Hyperf\Di\Aop\ProceedingJoinPoint;

/**
 * @Aspect()
 * 注意要在切面类上增加 @Aspect() 注解，且要 use 相应的命名空间
 */
class FooAspect extends AbstractAspect
{
    public $classes = [
    	//注意格式为 类名::方法名
        IndexController::class . '::' . 'index',
    ];

    public $annotations = [
    ];

    /**
     * @inheritDoc
     */
    public function process(ProceedingJoinPoint $proceedingJoinPoint)
    {
        var_dump(__CLASS__);
        $result = $proceedingJoinPoint->process();
        return $result;
    }
}
```

#### 注解形式
```PHP
<?php
namespace App\Aspect;

use App\Annotation\Foo;
use Hyperf\Di\Annotation\Aspect;
use Hyperf\Di\Aop\AbstractAspect;
use Hyperf\Di\Aop\ProceedingJoinPoint;

/**
 * @Aspect()
 * 注意要在切面类上增加 @Aspect() 注解，且要 use 相应的命名空间
 */
class FooAspect extends AbstractAspect
{
    public $classes = [
    ];

    public $annotations = [
    	//需要切入的注解
    	//实际需要切入的类(或方法、属性等)上需要添加相应注解
        Foo::class
    ];

    /**
     * @inheritDoc
     */
    public function process(ProceedingJoinPoint $proceedingJoinPoint)
    {
        $result = $proceedingJoinPoint->process();
        /** @var Foo $foo */
        $foo = $proceedingJoinPoint->getAnnotationMetadata()->class[Foo::class];	//该方法可以获取到代理后的实例
        $bar = $foo->bar;
        $result['message'] .= $bar;

        return $result;
    }
}
```

## 协程
协程是一种轻量级的线程，由用户代码来调度和管理，而不是由操作系统内核来进行调度，也就是在用户态进行。可以直接的理解为就是一个非标准的线程实现，但什么时候切换由用户自己来实现，而不是由操作系统分配 CPU 时间决定。
Swoole 的每个 Worker 进程 会存在一个协程调度器来调度协程，协程切换的时机就是遇到 I/O 操作或代码显性切换时，进程内以单线程的形式运行协程，也就意味着一个进程内同一时间只会有一个协程在运行且切换时机明确，也就无需处理像多线程编程下的各种同步锁的问题。

### 协程 vs 普通线程
- 共同点
	- 都有自己的上下文
	- 可以共享全局变量
- 线程
	- 在同一时间可以有多个线程处于运行状态
	- 普通线程是抢占式的，哪个线程能得到资源由操作系统决定
- swoole协程
	- 在同一时间只能有一个 Swoole 协程处于运行状态，其它的协程都会处于暂停的状态
	- 协程是协作式的，执行权由用户态自行分配。

### 协程编程注意事项
- 不能存在阻塞代码
	- 协程内代码的阻塞会导致协程调度器无法切换到另一个协程继续执行代码
	- 一键协程化的方法 \Swoole\Runtime::enableCoroutine()
	- [swoole 文档](https://wiki.swoole.com/#/runtime)
- 不能通过全局变量储存状态
	- 在 Swoole 的持久化应用下，一个 Worker 内的全局变量是 Worker 内共享的
	- 一个 Worker 会在一个时间周期内同时处理多个协程的代码，也就意味着如果使用了全局变量来储存状态可能会被多个协程所使用
	- 这里全局变量指的是 $_GET/$_POST/$_REQUEST/$_SESSION/$_COOKIE/$_SERVER等$_开头的变量、global 变量，以及 static 静态属性
- 最大协程数限制
	- 对 Swoole Server 通过 set 方法设置 max_coroutine 参数，用于配置一个 Worker 进程最多可存在的协程数量。
	- 因为随着 Worker 进程处理的协程数目的增加，其对应占用的内存也会随之增加，为了避免超出 PHP 的 memory_limit 限制，请根据实际业务的压测结果设置该值
	- Swoole 的默认值为 3000, 在 hyperf-skeleton 项目中默认设置为 100000

### Hyperf 里使用全局变量
- Hyperf所有的全局变量均可以在 请求(Request) 对象中得到相关的值。
- 对于 global 变量和 static 变量，在 PHP-FPM 模式下，本质都是存活于一个请求生命周期内的。
- 在 Hyperf 内因为是 CLI 应用，会存在 全局周期 和 请求周期(协程周期) 两种长生命周期
	- 全局周期，只需要创建一个静态变量供全局调用即可，静态变量意味着在服务启动后，任意协程和代码逻辑均共享此静态变量内的数据，也就意味着存放的数据不能是特别服务于某一个请求或某一个协程；
	- 协程周期，由于 Hyperf 会为每个请求自动创建一个协程来处理，那么一个协程周期在此也可以理解为一个请求周期，在协程内，所有的状态数据均应存放于 Hyperf\Utils\Context 类中，通过该类的 get、set 来读取和存储任意结构的数据，这个 Context(协程上下文) 类在执行任意协程时读取或存储的数据都是仅限对应的协程的，同时在协程结束时也会自动销毁相关的上下文数据。

### 协程使用方法
- 创建协程
	- co(callable $callable)
	- go(callable $callable)
	- Hyperf\Utils\Coroutine::create(callable $callable
- 判断当前是否处于协程环境内
	- Hyperf\Utils\Coroutine::inCoroutine(): bool
- 获得当前协程的 ID
	- Hyperf\Utils\Coroutine::id(): int //如不处于协程环境下，会返回 -1
- 更多特性
	- Channel 通道
		- Channel 可为多生产者协程和多消费者协程模式提供支持, 主要用于协程间通讯
		- Channel->push : 当队列中有其他协程正在等待 pop 数据时，自动按顺序唤醒一个消费者协程。当队列已满时自动 yield 让出控制权，等待其他协程消费数据
		- Channel->pop : 当队列为空时自动 yield，等待其他协程生产数据。消费数据后，队列可写入新的数据，自动按顺序唤醒一个生产者协程
	- WaitGroup
		- 使得主协程一直阻塞等待直到所有相关的子协程都已经完成了任务后再继续运行，这里说到的阻塞等待是仅对于主协程（即当前协程）来说的，并不会阻塞当前进程。
	- Parallel
		- Parallel 特性是 Hyperf 基于 WaitGroup 特性抽象出来的一个更便捷的使用方法
		- 限制 Parallel 最大同时运行的协程数
			- new Parallel(MAX_CO_NUM);
	- Concurrent 协程运行控制
		- @tod
	- 协程上下文
		- @todo
	- Swoole Runtime Hook Level
		- @todo
	- defer
		- @todo

### 示例
#### Channel
```PHP
co(function () {
    $channel = new \Swoole\Coroutine\Channel();
    co(function () use ($channel) {
        $channel->push('data');
    });
    $data = $channel->pop();
});
```
#### WaitGroup
```PHP
$wg = new \Hyperf\Utils\WaitGroup();
// 计数器加二
$wg->add(2);
// 创建协程 A
co(function () use ($wg) {
    // some code
    // 计数器减一
    $wg->done();
});
// 创建协程 B
co(function () use ($wg) {
    // some code
    // 计数器减一
    $wg->done();
});
// 等待协程 A 和协程 B 运行完成
$wg->wait();
```
#### Parallel
```PHP
<?php
use Hyperf\Utils\Exception\ParallelExecutionException;
use Hyperf\Utils\Coroutine;
use Hyperf\Utils\Parallel;

$parallel = new Parallel();
$parallel->add(function () {
    sleep(1);
    return Coroutine::id();
});
$parallel->add(function () {
    sleep(1);
    return Coroutine::id();
});

try{
    // $results 结果为 [1, 2]
   $results = $parallel->wait(); 
} catch(ParallelExecutionException $e){
    // $e->getResults() 获取协程中的返回值。
    // $e->getThrowables() 获取协程中出现的异常。
}
```
#### parallel()
```PHP
<?php
use Hyperf\Utils\Coroutine;

// 传递的数组参数您也可以带上 key 便于区分子协程，返回的结果也会根据 key 返回对应的结果
$result = parallel([
    function () {
        sleep(1);
        return Coroutine::id();
    },
    function () {
        sleep(1);
        return Coroutine::id();
    }
]);
```

## 配置
- 设置配置
	- 在 config/config.php 与 config/autoload/server.php 与 autoload 文件夹内的配置
- 获取配置
	- 通过 Config 对象获取配置
	- 通过 @Value 注解获取配置
		- 这种方式要求注解的应用对象必须是通过 hyperf/di 组件创建的
		- @Value("config.key")
	- 通过 config 函数获取
		- config(string $key, $default)
- 环境变量
	- env()


## 中间件
### 原理
- PSR15
- 主要用于编织从 请求(Request) 到 响应(Response) 的整个流程，通过对多个中间件的组织，使数据的流动按我们预定的方式进行
- 洋葱模型
- Request -> Middleware 1 -> Middleware 2 -> Middleware 3 -> Middleware 2 -> Middleware 1 -> Response

### 使用
1. 生成中间件
- php ./bin/hyperf.php gen:middleware Auth/FooMiddleware
2. 定义中间件
- 定义局部中间件
	- 通过注解定义
		- @Middleware 注解为定义单个中间件时使用，在一个地方仅可定义一个该注解，不可重复定义
		- @Middlewares 注解为定义多个中间件时使用，在一个地方仅可定义一个该注解，然后通过在该注解内定义多个 @Middleware 注解实现多个中间件的定义
		- 定义方法级别的中间件
			- 将注解加在方法上即可
			- 类级别上的中间件会优先于方法级别的中间件
	- 通过配置文件定义
		- Hyperf\HttpServer\Router\Router 类的每个定义路由的方法的最后一个参数 $options 都将接收一个数组，可通过传递键值 middleware 及一个数组值来定义该路由的中间件
- 定义全局中间件
	- 修改配置文件 config/autoload/middlewares.php 配置
	- 全局中间件执行顺序优先于局部中间件
	- ps: 实测全局定义了A中间件，又局部定义了A中间件，会导致局部定义的顺序在A中间件之后的其他中间件失效

#### 示例
```PHP
//通过注解定义中间件
<?php

namespace App\Controller;

use Hyperf\HttpServer\Annotation\AutoController;
use Hyperf\HttpServer\Annotation\Middleware;
use Hyperf\HttpServer\Annotation\Middlewares;
use App\Middleware\FooMiddleware;
use App\Middleware\BarMiddleware;
use App\Middleware\BazMiddleware;

/**
 * @AutoController()
 * @Middlewares(
 *     @Middleware(FooMiddleware::class),
 *     @Middleware(BarMiddleware::class),
 *     @Middleware(BazMiddleware::class)
 * )
 */
class TestController
{
    public function index() {
        return 'index';
    }
}
```

