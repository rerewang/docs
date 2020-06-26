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