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
### 在spring中使用 mongo

