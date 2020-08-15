# 微服务
## jsonRPC
### 依赖
- composer require hyperf/json-rpc
- composer require hyperf/rpc-server
- composer require hyperf/rpc-client

### 使用
#### 定义服务提供者
- 目前仅支持通过注解的形式来定义 服务提供者(ServiceProvider)
- @RpcService
	- name 属性为定义该服务的名称，这里定义一个全局唯一的名字即可，Hyperf 会根据该属性生成对应的 ID 注册到服务中心去；
	- protocol 属性为定义该服务暴露的协议，目前仅支持 jsonrpc 和 jsonrpc-http
	- server 属性为绑定该服务类发布所要承载的 Server，默认值为 jsonrpc-http，该属性对应 config/autoload/server.php 文件内 servers 下所对应的 name，这里也就意味着我们需要定义一个对应的 Server
	- publishTo 属性为定义该服务所要发布的服务中心，目前仅支持 consul 或为空
	- 使用 @RpcService 注解需 use Hyperf\RpcServer\Annotation\RpcService; 命名空间
- 定义 JSON RPC Server
	- [配置 config/autoload/server.php](https://hyperf.wiki/2.0/#/zh-cn/json-rpc?id=%e5%ae%9a%e4%b9%89-json-rpc-server)
- 发布到服务中心
	- 目前仅支持发布服务到 consul
	- [config/autoload/consul.php](https://hyperf.wiki/2.0/#/zh-cn/json-rpc?id=%e5%8f%91%e5%b8%83%e5%88%b0%e6%9c%8d%e5%8a%a1%e4%b8%ad%e5%bf%83)
- 定义服务消费者
	- 自动创建代理消费者类
		- [config/autoload/services.php](https://hyperf.wiki/2.0/#/zh-cn/json-rpc?id=%e8%87%aa%e5%8a%a8%e5%88%9b%e5%bb%ba%e4%bb%a3%e7%90%86%e6%b6%88%e8%b4%b9%e8%80%85%e7%b1%bb)
	- 手动创建消费者类
		- 手动创建一个消费者类
		- 配置 config/autoload/services.php 定义一个配置标记要从何服务中心获取节点信息
		- [示例](https://hyperf.wiki/2.0/#/zh-cn/json-rpc?id=%e6%89%8b%e5%8a%a8%e5%88%9b%e5%bb%ba%e6%b6%88%e8%b4%b9%e8%80%85%e7%b1%bb)
- more
	- @todo