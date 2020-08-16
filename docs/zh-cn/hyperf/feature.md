# 基础功能

## 异常处理
在 Hyperf 里，业务代码都运行在 Worker 进程 上，也就意味着一旦任意一个请求的业务存在没有捕获处理的异常的话，都会导致对应的 Worker 进程 被中断退出。
我们可以通过对各个 server 定义不同的 异常处理器(ExceptionHandler)，一旦业务流程存在没有捕获的异常，都会被传递到已注册的 异常处理器(ExceptionHandler) 去处理。

### 自定义异常处理
- 注册异常处理器
	- 仅支持配置文件的形式
		- config/autoload/exceptions.php
		- 每个异常处理器配置数组的顺序决定了异常在处理器间传递的顺序。
		- $this->stopPropagation() 停止异常传播
- 定义异常处理器
	- 继承抽象类 Hyperf\ExceptionHandler\ExceptionHandler
- 集成 Whoops
	- 安装 Whoops
		- composer require --dev filp/whoops
	- 配置 Whoops 专用异常处理器
		- config/autoload/exceptions.php 中配置 \Hyperf\ExceptionHandler\Handler\WhoopsExceptionHandler::class
- Error 监听器
	- 框架提供了 error_reporting() 错误级别的监听器 Hyperf\ExceptionHandler\Listener\ErrorExceptionHandler
	- 配置监听器
		- 在 config/autoload/listeners.php 中添加监听器