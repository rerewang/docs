# Mongo

## docker 运行 mongo
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
## mongo命令
数据库操作
```mongo
#使用数据库/创建数据库，当你使用一个不存在的mongo数据库时,就自动创建了一个mongo数据库
use db_name
#查看所有数据库
show dbs
#删除当前正在使用的数据库
db.dropDatabase()
#查看数据库信息
db 	//查看当前所使用的数据库名
db.stats()	//查看当前数据库状态
db.version()	//查看当前数据库版本

#创建用户
db.createUser( {user: "user_name",pwd: "password",roles: [ { role: "role", db: "db_name" } ]})
#查看用户
show users

#创建集合
db.createCollection(collection_name, { size : ..., capped : ..., max : ... } )
#查看当前所在数据库
show tables | show collections
#删除当前正在使用的数据库
db.collection_name.drop()
#使用集合
db.collection_name.find()  # 查所有满足条件数据
db.collection_name.findOne()  # 查满足条件的一条数据
db.collection_name.count()  # 统计集合下面有多少数量的数据

#增删改查
#插入记录，当你往一个空集合里面插入了一条数据之后就自动创建了一个集合
db.collection_name.insert({json})


#备份数据库
#导出
mongodump --host IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件路径
#导入
mongoorestore --host --port -d 文件路径
```