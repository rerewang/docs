# vagrant
## www
### what
vagrant基于Ruby语言开发，vagrant是构建在虚拟化技术之上的 *虚拟化运行环境管理工具*
- Vagrant的运行，需要依赖于某项具体的虚拟化技术, eg: virtualbox、vmware
- vagrant只是一个管理工具，并不是某项具体的虚拟化技术

### why
- 跨平台
- 统一开发环境
- 自动部署，无需人工参与

## 安装部署
### windows
- 安装virtualbox
- 安装vagrant
- 安装xshell

## 常见命令
### box
#### vagrant box
用来管理（添加、删除）boxes的命令
- vagrant box list 	查看目前已有的boxes
- vagrant box add 	新增一个box [寻找想要的boxes](https://app.vagrantup.com/boxes/search)
- vagrant box remove NAME 	删除指定的box
- vagrant box outdated 	查看已过期的box(这里一定要进入拥有Vagrantfile的目录下执行这条命令)
- vagrant box update 	更新box(这里一定要进入拥有Vagrantfile的目录下执行这条命令)

### 虚拟机相关命令
- vagrant init [name [url]] 	初始化一个虚拟机的配置，在相应目录下，会生成一个Vagrantfile文件
- vagrant up 	启动虚拟机
- vagrant ssh 	ssh登录到虚拟机
- vagrant suspend 	挂起虚拟机
- vagrant reload 	重启虚拟机
- vagrant halt 	关闭虚拟机
- vagrant destroy 	删除虚拟机


## 文件同步
### synced_folder
### NFS
### rsync

## Vagrantfile
- config.vm.box 	指定使用哪个box
- config.vm.hostname 	机器主机名
- config.vm.network 	虚拟机网络设置
- config.vm.synced_folder 	同步目录设置
- config.vm.network 	端口转发设置

### network
```vagrantfile
//私有网络只允许主机访问不允许其他机器访问
config.vm.network "private_network", ip: "192.168.10.11"

//共有网络 类似局域网中单独的服务器，允许其他机器访问
config.vm.network "public_network", ip: "192.168.31.190"

//配置当前vm的host-only网络的IP地址为192.168.33.10
config.vm.network :private_network, ip: "192.168.33.10"

//私有网络的IP可以不指定，而是采用dhcp自动生成的方式，如 :
config.vm.network "private_network", type: "dhcp”

//创建一个公共网络，指定IP
config.vm.network "public_network", ip: "192.168.0.17"

//创建一个公共网络，指定桥接适配器
config.vm.network "public_network", bridge: "en1: Wi-Fi (AirPort)"

//创建一个公共网络，不指定桥接适配器
config.vm.network "public_network"
```