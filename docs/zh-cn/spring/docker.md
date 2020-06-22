# docker

## 命令

### 镜像相关
- docker pull <image>   //下载镜像
- docker search <image>		//dockerhub 上查找

### 容器相关
- docker run
	- -d 	//后台运行容器
	- -e 	//设置环境变量
	- -expose / -p 宿主端口:容器端口   //端口映射
	- -name 	//指定容器名称
	- --link 	//链接不同容器
	- -v 宿主目录:容器目录		//挂载磁盘卷
- docker start/stop <容器名>
- docker ps  <容器名>
- docker log  <容器名>
