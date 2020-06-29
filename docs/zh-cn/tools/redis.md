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