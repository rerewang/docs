#redis

## docker 运行 mongo
```shell
#获取镜像
docker pull redis
#运行redis镜像
docker run --name redis -p 6379:6379 -d redis
#登录到redis容器中
docker exec -it redis bash
```

## 解决方案
- 高可用
	- 主从
	- 哨兵
	- 集群
- 扩容
	- 分区
	- 集群

## 分区
分区是最简单的一种扩容方案，思路就跟关系型数据分区一样，通过一定的算法将key分成不同的范围放入对应的实例中。通常可以采用：
- 客户端处理 (范围区分、hash计算等)
- 代理中间件 

### 优缺点
- 优点
	- 简单
- 缺点
	- 无法进行多key操作
	- 维护成本高，缺乏统一管理
	- 灵活性有限，想要动态调整redis实例数量，需要做大量的数据迁移

## 主从复制
主从复制并没有实现高可用，但是为高可用打下了基础。原理就是主节点将数据同步至从节点。
顺便可以做读写分离。
### 缺点：
- salve节点都是只读的，无法支持写流量大的场景
- 故障转移不友好，master节点骨雕后，需要手动设置。

## 哨兵 Sentinel
Redis Sentinel 是redis的一种高可用方案。哨兵是一个独立的进程，作为进程独立运行。
原理:哨兵通过发送命令，等待redis服务器响应，从而监控运行的多个reids实例.
哨兵的作用：监控、故障转移、通知、服务发现

### 哨兵是怎样感知整个系统中的所有节点(master节点/slave节点/sentinel节点)的
1. 首先master节点的信息是配置在sentinel节点的配置文件中的
2. sentinel节点会和配置的master节点建立两条链接 *命令链接* 和 *订阅链接*
3. sentinel节点会通过 *命令链接* 每10s发送一次 *info* 命令，通过 *info* 命令，master节点会返回自己的 *run_id* 和 *slave节点信息*
4. sentinel节点会对这些 slave 节点也建立两条链接 *命令链接* 和 *订阅链接*
5. sentinel节点会通过 *命令链接* 向slave节点发送 *info* 命令，获取salve节点的一些信息 *run_id*、*role*、*复制偏移量offset* 等
6. 由于sentinel节点和其他节点(master/slave)都建有 *命令链接* 和 *订阅链接*，
	- a. 通过 *命令链接* 向其他节点的 *_sentinel:hello* 频道发送一条消息，内容包括自己的 *ip/port*、*run_id*、*配置纪元current_epoch* 等
	- b. 通过 *命令链接* 对其他节点的 *_sentinel:hello* 频道做了监听(所以所有向该频道发送的哨兵消息都能被接受到)
	- c. 解析监听到的消息，通过分析提取，就可以知道还有哪些sentinel节点也在监听这些服务节点了，更新结构体将这些sentinel节点记录下来
7. sentinel节点会向观察到的其他sentinel节点建立 *命令链接* (没有订阅链接)


### 多哨兵模式下故障转移(failover)
1. 主观下线
	- sentinel节点会已每秒一次的频率向建立了命令链接的实例发送ping命令， 如果在 *down-after-millseconds* 毫秒内没有做出有效相应(包括PONG/LOADING/MASTERDOWN)以外的响应，sentinel就会将该实例在本结构体中的状态标记为 *SRI_S_DOWN* 主管下线
2. 客观下线
	- 当一个sentinel节点发现master节点处于 *主观下线* 状态时，会向其他的sentinel节点发出询问，该节点是否已经主观下线了。如果超过配置参数 *quorum* 个节点认为是主观下线时，该sentinel节点就位将自己维护的结构体中该master节点标记为 *SRI_O_DOWN* 客观下线
	- 询问命令 *SENTINEL is-master-down-by_addr <ip> <port> <current_epoch> <run_id>*，参数说明如下
		- ip/port 当前认为下线的master节点的ip和端口
		- current_epoch 配置纪元
		- run_id 询问时用* ，选举时用run_id （有值表示该哨兵节点希望对方将自己设置为leader）
3. leader选举
	- 在认为master节点 *客观下线* 时，sentinel节点间会发起一次选举，选举命令同询问命令 *SENTINEL is-master-down-by_addr <ip> <port> <current_epoch> <run_id>*，只是参数*run_id* 会将自己的run_id带进去，希望接受者将自己设置为主节点，如果超过半数以上的节点返回将改节点标记为leader的情况下，该leader会对故障进行迁移
4. 故障转移
	- 在slave节点中选出新的master节点
		- 通讯正常
		- 优先级排序
		- 优先级相同选择offset最大的
	- 将该节点设置为新的master节点 *SLAVEOF no one*, 并确保在后续的INGO命令时，该节点的返回状态为master
	- 将其他的slave节点设置为从新的master节点复制， *SLAVEOF命令*
	- 将旧的master节点变成新的master节点的slave节点

### 优缺点
- 优点
	- 简单
	- 高可用，在主节点故障时能够实现故障的迁移
- 缺点
	- 本质上是主从，每台redis服务器都存储相同的数据，浪费内存，无法做水平扩展
	- 故障转移期间短暂的不可用
	

## 集群
### redis cluster
- 数据自动分片(分成16384个  Hash Slot 哈希槽) 每个实例负责一部分槽,*CRC16(key) & 16383* 计算key所对应的槽位
- 在部分节点失效后有一定的可用性
