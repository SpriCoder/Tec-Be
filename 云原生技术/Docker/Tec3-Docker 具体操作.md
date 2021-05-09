Tec3-Docker 零散操作
---

# 1. Docker 组成
1. 容器是在Linux内核实现的轻量级隔离机制
   1. 虚拟机是操作系统级别的资源隔离
   2. 容器本质上是进程级的资源隔离，是共用主机内核，利用内核的虚拟化技术隔离出一个独立的运行环境，拥有独立的一个文件系统、网络空间和进程空间视图
2. 组成
   1. Docker Daemon(Docker Engine)：一般在宿主主机后台运行
   2. 用户使用Client和Engine(Daemon)交互，可以通过pip、unix socket、tcp进行交互
   3. Docker Index指向Docker registries，也叫Docker仓库，也叫做Docker仓库，可以用来上传和下载images，官方docker仓库:Hub.docker.com
3. 使用GO语言实现

## 1.1. 虚拟机与容器
1. 从虚拟化层看容器，轻量级、高性能是核心价值：虚拟机 vs 容器
   1. 容器是在Linux内核实现的轻量级资源隔离机制
   2. 虚拟机是操作系统级别的资源隔离，容器本质上是进程级的资源隔离

## 1.2. 使用
1. run
   + -d：后台运行容器，并返回容器ID
   + -i：以交互模式运行容器，通常与-t同时使用
   + -t：为容器重新分配一个伪输入终端，通常与-i同时使用
   + -p：指定(发布)端口映射，格式(主机(宿主)端口:容器端口)
   + -P：随机端口映射，容器内部端口随机映射到主机的高端口
   + --name = "docker_name"：为容器指定一个名称
   + -e env="value"：设置环境变量
   + --env-file="filename"：从指定文件中读入环境变量
   + --expose=2000-2002：开放(暴露)一个端口或一组端口
   + --link container_name:tag_name：添加链接到另一个容器，对于某个容器，跑的时候给要连接的另一个容器添加一个名字，就可以把那个容器映射到当前容器里
   + -v path_name：绑定一个卷(volumn)
   + --rm：退出时自动删除容器
2. container
   + `docker container ls`
   + `docker container ls -a`
   + `docker container ls -aq`
   + `docker inspect` 容器名：显示容器信息
   + `docker port` 容器名：显示端口映射信息
   + `docker logs -f <contioner id>` 查看容器内部的标准输出
   + `docker stop <container id>` 停止一个容器
   + `docker start <container id>` 启动已停止运行的容器
   + `docker restart <container id>` 正在运行的容器可以重启
   + `docker attach <container id>` 附着到容器
   + `docker exec -it <container id>` /bin/bash 进入容器，执行命令
   + `docker rm -f <container id>` 删除容器
   + `docker container prue`：清理掉所有处于终止状态的容器

## 1.3. Volumn
1. 镜像分层
   1. 镜像顶层可读写，底层不可写
   2. 写时复制：如果需要进行读写，进行提升
2. 数据卷(volume：Docker管理宿主机文件系统的一部分，默认位于/var/lib/docker/volumns目录中)
   1. Docker 管理卷：`docker volumn 命令`
   2. 绑定挂载卷：`-v`，挂载到容器内
   3. bind mounts:意味着可以存储在宿主机系统的任意位置
   4. tmpfs：挂载存储在宿主机系统的内存中

```sh
# docker volume create edc-nginx-vol // 创建一个自定义容器卷 
# docker volume ls // 查看所有容器卷
# docker volume inspect edc-nginx-vol // 查看指定容器卷详情信息
docker inspect -f {{.Mounts}} b1
#docker volume rm edc-nginx-vol // 删除自定义数据卷
创建使用指定卷的容器
docker run -d --name=edc-nginx -p 8800:80 -v edc-nginx-vol:/usr/share/nginx/html nginx   注意：-v 后面只有一个目录名，则指的是在容器中目录，然后会自动创建一个volume与它对应。如果edc-nginx-vol不存在，则会自动创建一个volume
docker run -d  --name=edc-nginx -v /app/wwwroot:/usr/share/nginx/html nginx

docker rm -v 命令在删除容器时删除该容器的卷
docker volume prune

docker cp c:/temp1 first:/data    在主机与容器之间COPY
docker cp first:/data  /tmp
```

## 1.4. 导出和导入容器镜像
```sh
docker export 1e560fca3906 > ubuntu.tar
# 导出容器 1e560fca3906 快照到本地文件 ubuntu.tar
docker import ubuntu.tar test/ubuntu:v1
# 将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1
docker import http://example.com/exampleimage.tgz example/imagerepo
```

## 1.5. 容器网络
1. `docker bridge`：所有的容器都联通到桥上，彼此间就可以连通。
   1. 桥有自己的网关，可以路由到虚拟机的网卡上，虚拟机又可以路由到外面的宿主机
   2. 两个虚拟机之间可以用overlay打通。
   3. 不建议挂载bridge上，因为需要`--link`才能彼此连通。
2. 容器网络
   1. none网络，--net=none
   2. host网络，--net=host
   3. bridge网络，--net=bridge，docker0 的 linux bridge
   4. container模式，--net=container:NAME_or_ID
3. `host`：容器本身没有网卡，使用的是宿主机的网络。
4. 使用自己创建的网络
   1. 同一个网络下的各个容器，可以通过容器名直接连通
   2. 也可以到达虚拟机的网卡，连接宿主机
5. 自己创建的网络和真实的网络是一样的
6. 一个容器可以有多个网卡(IP地址)

```shell
connect  NETWORK CONTAINER    Connect a container to a network
create      Create a network
disconnect  NETWORK CONTAINER  Disconnect a container from a network
inspect     Display detailed information on one or more networks
ls          List networks
prune       Remove all unused networks
rm          Remove one or more networks
docker network create my-network
docker run --net=my-network --name mymongo -d mongo 
docker run --net=my-network  -it  --rm mongo mongo --host mymongo
# Docker 挂载网络
```

# 2. Docker删除所有无名称镜像(悬空镜像)
1. 针对镜像名为`<none>`的镜像
2. 使用命令：`docker rmi $(docker images -f "dangling=true" -q)`

# 3. 参考
1. <a href = "https://www.cnblogs.com/stulzq/p/8962388.html">Docker 删除所有无名称的镜像（悬空镜像）</a>