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

## 镜像相关
### 国内镜像加速

### Docker镜像
- 镜像是静态的只读模板
- 镜像中包含构建docker容器的命令
- 镜像是分层的
- 通过Dockerfile来创建镜像

### Dockerfile
@todo

### 通过Maven 构建 Docker 镜像
- 准备工作
	- 提供一个 Dockerfile
	- 配置 dockerfile-maven-plugin 插件
- 执行构建
	- mvn package
	- mvn dockerfile:build
- 检查结果
	- docker images