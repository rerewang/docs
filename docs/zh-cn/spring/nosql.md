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
- @EnableCaching	//开启缓存
	- proxyTargetClass 是否使用CGLIB代理，而不是默认的java接口代理。默认是false。决定是基于接口还是基于类的代理被创建
	- mode  缓存的代理模式，AdviceMode.PROXY(默认) / AdviceMode.ASPECTJ. 
	- order 缓存 
- @Cacheable	//在方法前使用，将运行结果缓存；当标记在一个类上，表示该类的所有方法都支持缓存
	- *当一个支持缓存的方法在对象内部被调用时，是不会触发缓存的*
	- value 缓存的名称，在spring配置文件中定义，必须指定至少一个
- @CacheEvict  	//缓存清理
- @CachePut		//缓存设置，不会检查缓存是否存在，每次都会执行该方法，并将结果写入指定的缓存中
- @Caching 		//可以同时指定多个SpringCache相关的注解
	- 三个属性 cacheable、put、evict 分别用于指定 @Cacheable、@CachePut、@Caching
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


