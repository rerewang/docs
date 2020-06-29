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

### 注解
