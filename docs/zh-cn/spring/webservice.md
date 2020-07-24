# Web Service
## Restful Web Service
### Richardson 成熟度模型
- Lv0: The Swamp of POX
- Lv1: Rsources
- Lv2: HTTP Verbs
- Lv3: Hpyermeida Controls
- Glory of REST
 
### 如何实现 Restful Web Service
#### 识别资源
- 找到领域名词
	- 能用CURD操作的名词
- 将资源组织成集合（即集合资源）
- 将资源合并为复合资源
- 计算或处理函数

#### 资源的粒度
- 站在服务端的角度，要考虑
	- 网络效率
	- 表述的多少
	- 客户端的易用程度
- 站在客户端的角度，要考虑
	- 可缓存性
	- 修改频率
	- 可变性

#### 构建更好的URI
- 使用域及子域对资源进行合理的分组或划分
- 在URI的路径部分使用斜杠分隔符(/)来表示资源之间的层次关系
- 在URI的路径部分使用逗号(,)和分好(;)来表示非层次关系
- 使用连字符(-)和下划线(_)来改善长路径中名称的可读性
- 在URI的查询部分使用于符号(&)来分隔参数
- 在URI中避免出现文件扩展名(例如 .php .aspx 和 .jsp)


#### HTTP 方法
|动作|安全/幂等|用途|
|---|---|---|
|GET|Y/Y|信息获取|
|POST|N/N|用途广泛，用于创建、更新、一次性对多个资源进行更新等|
|DELETE|N/Y|删除资源|
|PUT|N/Y|更新或完全替换一个资源|
|HEAD|Y/Y|获取与GET一样的HTTP头信息，但没有响应体|
|OPTIONS|Y/Y|获取资源支持的HTTP方法列表|
|TRACE|Y/Y|让服务器返回其接收到的HTTP头|

#### HTTP状态码
@todo

#### 选择合适的表述
- JSON
- XML
- HTML
- ProtoBuf

### HATEOAS
Hypermeida As The Engine Of Application State

#### HATEOAS vs WSDL
- HATEOAS
	- 表述中的超链接会提供服务所需的各种REST接口信息
	- 无需事先约定如何访问服务
- 传统的服务契约
	- 必须事先约定服务的地址与格式

#### HATEOAS中常用的超链接类型
|REL|描述|
|self| 指向当前资源本身的链接 |
|edit| 指向一个可以编辑当前资源的链接 |
|collection| 如果当前资源包含在一个集合中，指向该集合的链接 |
|search| 指向一个可以搜索当前资源或者其他资源的链接 |
|related| 指向一个与当前资源相关的链接 |
|first| 集合遍历相关的类型，指向第一个资源的链接 |
|last| 集合遍历相关的类型，指向最后一个资源的链接 |
|previous| 集合遍历相关的类型，指向上一个资源的链接 |
|next| 集合遍历相关的类型，指向下一个资源的链接 |

### Spring Data REST
- Spring Boot 依赖
	- spring-boot-starter-data-rest
- 常用注解与类
	- @RepositoryRestResource
	- Resource<T>
	- PagedResource<T>

#### HAL
Hypertext Application Language， HAL是一种简单的格式，为API中的资源提供简单一致的链接

HAL 模型
- 链接
- 内嵌资源
- 状态