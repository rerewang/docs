# Nosql


## Mongo
### 在spring中使用 mongo
注解
- @Document
- @Id
MongoTemplate
- save/remove
- Criteria / Query / Update

#### 建库
```mongo
use springbucks;
db.createUser({
	user:"springbucks", 
	pwd:"springbucks", 
	roles: [
		{role:"readWrite", db:"springbucks"}
	]
});
```
#### 配置
```JAVA
spring.data.mongodb.uri=mongodb://springbucks:springbucks@localhost:27017/springbucks
```

#### Converter
@todo


## Redis
### Jedis
- Jedis 不是线程安全的
- 使用JedisPool获取Jedis实例
- 直接使用Jedis中的方法

#### 配置
```JAVA
redis.host=localhost
redis.maxTotal=5
redis.maxIdle=5
redis.testOnBorrow=true
```
#### 使用
##### JedisPool
##### jedisPoolConfig
##### Jedis 方法

#### Jedis哨兵模式
##### JedisSentinelPool
initSentinel()
initPoll(master)

#### Jedis集群模式
##### JedisCluster
只支持从master做读写，如果要读写分离，需要定制。


