# 1.Docker 组件
##1.1Docker服务器与客户端
##1.2Docker镜像和容器
	镜像相当于 java中的类, 容器相当于new 出来的对象. 一个镜像可以实例多个容器
##1.3 Registry(注册中心)
	Docker用Registry来保存用户构建的镜像。Registry分为公共和私有两种。Docker公司运营公共的Registry叫做Docker Hub。用户可以在Docker Hub注册账号，分享并保存自己的镜像(说明:在Docker Hub"下载镜像巨慢，可以自己构建私有的Registry)。
	https://hub.docker.com/
# 2.Docker 安装和启动
##2.1 安装Docker
Ubuntu是最先更新或者打补丁的。在很多版本的CentOS中是不支持更新最新的一些补丁包的。
由于我的环境都使用的是CentOS,因此这里我将Docker安装到CentOS上。注意:这里建
议安装在CentOS7.x以上的版本，在CentOS6.x的版本中，安装前需要安装其他很多的环境而且Docker很
多补丁不支持更新。

	(1) yum包更新到最新
	   sudo yum update
	(2)安装需要的软件包，yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依
	赖的
	   sudo yum install -y yum-utils device-mapper-persistent-data 1vm2
	(3)设置yum源为阿里云
	   sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
	(4)安装docker(ce-社区办  ee企业版(收费))
	   sudo yum install docker-ce    
	(5)安装后查看docker版本
   docker -V
##2.2 设置ustc的镜像
	ustc是老牌的linux镜像服务提供者了，还在遥远的ubuntu 5.04版本的时候就在用。ustc的docker镜像加
	速器速度很快。ustc docker mirror的优势之一就是不需要注册，是真正的公共服务。
	https://lug.ustc.edu.cn/wiki/mirrors/help/docker
编辑该文件:
vi /etc/ docker/daemon.json
在该文件中输入如下内容:
{
"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}

## 2.3 Docker的启动和停止
  systemctl 命令是系统服务管理器指令
    启动docker: .
	    systemctl start docker
	停止docker: .
	    systemctl stop docker
	重启docker:
	    systemctl restart docker
	查看docker状态:
     	systemctl status docker
	开机启动: 
	    systemctl enable docker
	重启docker:
	    systemctl restart
	查看docker概要信息
		docker --help
#3常用命令
##3.1镜像相关命令
###3.1.1查看镜像
   docker images(查看镜像)
		REPOSITORY:镜像名称
		TAG:镜像标签(一般是版本)
		IMAGE ID:镜像ID(唯一id)
		CREATED:镜像的创建日期(不是获取该镜像的日期)
		SIZE:镜像大小.
		这些镜像都是存储在Docker宿主机的/var/ib/docker目录下
###3.1.2搜索镜像
如果你需要从网络中查找需要的镜像，可以通过以下命令搜索
	docker search 镜像名称
		例如: docker search centos
		NAME:镜像名称
		DESCRIPTION:镜像描述
		STARS:用户评价，反应-一个镜像的受欢迎程度
		OFFICIAL:是否官方
		AUTOMATED:自动构建，表示该镜像由Docker Hub自动构建流程创建的	

###3.1.3拉取镜像
拉取镜像就是从中央仓库中下载镜像到本地
 docker pull 镜像名称 
	例如，我要下载centos7镜像
	docker pull centos:7 
###3.1.4删除镜像
按镜像 ID/名称 删除镜像
docker rmi 镜像ID/名称
删除所有镜像
docker rmi `docker images -q`     这个`是键盘Esc按键下的引号

#3.2 容器相关命令
## 3.2.1查看容器
查看正在运行的容器
  docker ps
查看所有容器(运行和不运行的容器)
  docker ps -a
查看最后一次运行的容器
  docker ps -l
查看停止的容器
  docker ps -f status=exited 

##3.2.2 创建与启动容器
创建容器常用的参数说明: 
创建容器命令: docker run
	-i:表示运行容器.

	-t: 表示容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端。

	--name :为创建的容器命名。

	-V:表示目录映射关系(前者是宿主机目录，后者是映射到宿主机上的目录)，可以使用多个-v做多个
	目录或文件映射。注意:最好做目录映射，在宿主机上做修改，然后共享到容器上。

	-d:在run后面加上-d参数,则会创建一个守护式容器在后台运行(这样创建容器后不会自动登录容器，如
	果只加-i -t两个参数，创建后就会自动进去容器)。

	-p:表示端口映射，前者是宿主机端口，后者是容器内的映射端口。可以使用多个-p做多个端口映射

(1)交互式方式创建容器
   docker run -it --name=容器名称 镜像名称:标签 /bin/bash
   这时我们通过ps命令查看，发现可以看到启动的容器，状态为启动状态
  退出当前容器
   exit
   当退出当前容器 此容器就关闭了
   
   
   
(2)守护式方式创建容器:
docker run -di --name=容器名称 镜像名称:标签
登录守护式容器方式(进入容器):
docker exec -it 容器名称(或者容器ID) /bin/bash

###3.2.3停止与启动容器
停止容器:
 docker stop 容器名称(或者容器ID)
启动容器:
 docker start 容器名称(或者容器ID)
 

###3.2.4文件拷贝
如果我们需要将文件拷贝  容器内可以使用cp命令
     docker cp 需要拷贝的文件或目录  容器名称:容器目录
也可以将文件从容器内拷贝出来
     docker cp 容器名称:容器目录  需要拷贝的文件或目录

###3.2.5目录挂载
我们可以在创建容器的时候，将宿主机的目录与容器内的目录进行映射，这样我们就可以通过修改宿主机
某个目录的文件从而去影响容器。
  创建容器添加-v参数后边为  宿主机目录:容器目录，例如:
  docker run -di -V /usr/1ocal/myhtml:/usr/local/myhtml --name=mycentos3 centos :7
如果你共享的是多级的目录，可能会出现权限不足的提示。
这是因为CentOS7中的安全模块selinux把权限禁掉了，我们需要添加参数-privileged=true 来解决挂载
的目录没有权限的问题

###3.2.6查看容器IP地址
我们可以通过以下命令查看容器运行的各种数据
  docker inspect 容器名称(容器ID)
也可以直接执行下面的命令直接输出IP地址
docker inspect --format='{{.NetworkSettings.IPAddress}}'  容器名称(容器ID)

###3.2.7删除容器.
  删除指定的容器:
  docker rm  容器名称(容器ID)
