# Nosql


## Mongo
### docker 运行 mongo
```shell
#获取镜像
docker pull mongo
#运行mongo镜像
docker run --name mongo -p 27017:27017 -v ~/docker-data/mongo:/data/db -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=admin -d mongo
#登录到mongo容器中
docker exec -it mongo bash
#链接到mongo (容器中执行)
mongo -u username -p password
```
### mongo命令
数据库信息
```mongo
show dbs
use dbname
```
数据库操作
```mongo
use dbname	//创建数据库
db.dropDatabase()	//删除当前数据库
db.test1.insert(json)	//插入记录
```

### 在spring中使用 mongo
