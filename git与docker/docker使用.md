# 4.docker使用

仓库用于存储镜像,容器是运行起来的镜像,容器commit操作可以创建新的镜像,可以理解镜像为类,容器是实例对象.一个镜像可以运行多个容器。

镜像是与容器层结构，底层无法直接修改

## 1.基本使用

+ 启动

```
service docker start |stop |restart |status #开始,停止,重启,状态
```

docker的应用目录存放在/var/lib/docker/

+ 镜像操作

```
#查看所有镜像  
	docker images |docker image ls 
#搜索镜像
	docker search 镜像名称
# 拉取镜像
	docker image pull 仓库名称/镜像名称 
# 删除镜像
	docker image rmi 镜像名或ID
	docker image rm 容器名或id
# 镜像重命名
	docker tag [old_image]:[old_version] [new_image]:[new_version]
	如果一个image_id存在多个名称，那么应该使用name:tag的格式删除镜像
# 将镜像导出到本地
	docker save -o [包文件] [镜像]
# 镜像导入到本地
	docker load < [image.tar_name]

```

```
#创建新镜像
1、从已经创建的容器中更新镜像，并且提交这个镜像
2、使用 Dockerfile 指令来创建一个新的镜像
runoob@runoob:~$ docker build -t runoob/centos:6.7 .
Sending build context to Docker daemon 17.92 kB
Step 1 : FROM centos:6.7
 ---&gt; d95b5ca17cc3
Step 2 : MAINTAINER Fisher "fisher@sudops.com"
 ---&gt; Using cache
 ---&gt; 0c92299c6f03
Step 3 : RUN /bin/echo 'root:123456' |chpasswd
 ---&gt; Using cache
 ---&gt; 0397ce2fbd0a
Step 4 : RUN useradd runoob
......
每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
第一条FROM，指定使用哪个镜像源
RUN 指令告诉docker 在镜像内执行命令，安装了什么。。。
然后，我们使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像。
```



+ 容器操作

```
# 查看容器
	docker ps |docker container ls --all # 查看正在运行的容器,查看所有容器
# 容器启动
	docker start|stop 容器名或id #启动关闭容器
# 删除 
	docker rm -f 容器 # 强制删除正在运行的容器
# 创建容器
	sudo docker run [option] 镜像名 [向启动容器中传入的命令]
	常用可选参数说明：
* -i 表示以《交互模式》运行容器。
* -t 表示容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端。
* --name 为创建的容器命名。
* -v 表示目录映射关系，即宿主机目录:容器中目录。注意:最好做目录映射，在宿主机上做修改，然后共享到容器上。 
* -d 会创建一个守护式容器在后台运行(这样创建容器后不会自动登录容器)。 
* -p 表示端口映射，即宿主机端口:容器中端口。
* --network=host 表示将主机的网络环境映射到容器中，使容器的网络与主机相同。
```

```
#进入容器
	docker exec -it 容器
	exit或ctrl+D退出
# 基于容器创建镜像
	docker commit -m '说明信息' -a "作者信息" [container_id] [new_image:tag]
#查看容器运行日志
	docker inspect [容器id]
```



