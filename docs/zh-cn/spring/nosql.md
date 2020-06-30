# Nosql

## Spring的缓存抽象
为不同的缓存提供一层抽象 （使用了AOP）
- 为java方法增加缓存，缓存执行结果
- 支持ConcurrentMap、EnCache、Caffeine、 JCache(JSR-107)
- 接口
	- org.springframework.cache.Cache
	- org.springframework.cache.CacheManger

### 依赖
```JAVA
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

### 注解
- @EnableCaching
	- @Cacheable
	- @CacheEvict  	//缓存清理
	- @CachePut		//缓存设置
	- @Caching 		//
	- @CacheConfig	//对缓存做设置


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


