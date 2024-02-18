
# Docker 常用命令

参考链接：

- 基础用法：[https://blog.csdn.net/weixin_57332537/article/details/129815299](https://blog.csdn.net/weixin_57332537/article/details/129815299)
- 高级用法：[https://zhuanlan.zhihu.com/p/653244081](https://zhuanlan.zhihu.com/p/653244081)

---

#### 重要概念

- image 镜像：已经稳定的容器，里面打包了所有可以用来发布的配置和可执行文件；
- container 容器：正在开发的环境，所有修改都应该在容器中完成，最后再打包成镜像；

每次运行命令 `docker run` 如果不指定 `--name` 则会生成一个匿名容器，这个容器的配置就是镜像的配置；

运行命令 `docker start` 的时候才是运行想要使用的容器，通常还需要

---

#### run命令常用参数

- -i：interaction 以交互模式打开 ；
- -t：terminal 分配一个终端；
- -v：目录映射，容器目录挂载到宿主机目录，格式： <host目录>:<容器目录>；
- -d：daemon 守护进程，后台维持这个容器运行；
- –name：容器名字；
- -p：port 指定端口映射，格式：主机(宿主)端口:容器端口；
- -P：port 随机端口映射，容器内部端口随机映射到主机的端口（49153起始 49153到65535）；

也可以用组合方式：

```
$ docker run -dit ubuntu:18.04 bash
```

---

#### 查看所有可用的容器

```
$ docker ps -a
```

#### 以某个镜像为基础启动一个容器

```
$ docker run --name <容器名> <镜像名:tag>
$ docker run --name test_container ubuntu:18.04
$ docker run -dit --name ubuntu-ros ubuntu-ssh:latest bash
```

#### （推荐操作）

进入容器后编辑 `~/.bashrc` 文件以彩色显示终端。

```
$ force_color_prompt=yes  # 删除注释
```

#### 运行一个容器并进入这个容器

```
$ docker start <容器名 or ID>
$ docker start ubuntu-orig
```

```
$ docker exec -it <容器名 or ID> <进入方式>
$ docker exec -it ubuntu-orig bash
```

#### 停止/杀掉一个容器

```
$ docker stop/kill <容器名 or ID>
$ docker stop ubuntu-orig
$ docker kill ubunut-orig
```

#### 将一个容器发布成镜像

```
$ docker commit <容器名 or ID> <新的镜像名>
$ docker commit ubuntu-ros ubuntu-ros-image
```

---

#### 查看可用的镜像

```
$ docker image ls
REPOSITORY   TAG               IMAGE ID       CREATED        SIZE
osrf/ros     melodic-desktop   3615ac14882c   33 hours ago   1.98GB
ubuntu       18.04             d1a528908992   6 months ago   56.7MB
```

---

#### 查看可用的卷

```
$ docker volume ls
DRIVER    VOLUME NAME
local     ubuntu_ros
```

---

#### 查看正在运行的容器

```
$ docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES
fa5431251872   ubuntu:18.04   "/bin/bash"   15 minutes ago   Up 15 minutes             kind_mclean
```

---

#### 进入一个正在运行的容器

1. 首先查看哪些容器正在运行

```
$ docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES
fa5431251872   ubuntu:18.04   "/bin/bash"   12 minutes ago   Up 12 minutes             kind_mclean
```

2. 进入某个正在运行的容器

```
$  docker exec -it fa5431251872 bash
```

---

#### 创建一个新的数据卷

```
$ docker volume create ubuntu_ros
```

---

#### 查看指定数据卷位置

```
$  docker volume inspect ubuntu_ros
[
    {
        "CreatedAt": "2023-12-16T03:12:08Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/ubuntu_ros/_data",
        "Name": "ubuntu_ros",
        "Options": null,
        "Scope": "local"
    }
]
```

---

#### 创建一个容器并挂载数据卷

以 `bash` 方式运行容器 `ubuntu:18.0` 并将 `ubuntu_ros` 数据卷挂载在容器的 `/data` 目录下。

```
$ docker run -it -v ubuntu_ros:/data ubuntu:18.04 bash
```

执行完后最好再查看下数据卷是否被成功挂载:

```
$ docker inspect ubuntu:18.04
```

---

#### 创建一个新容器并随机映射一个主机端口给容器用于网络通讯

```
$ docker run -P --name tomcat_muller tomcat:7
```

---

#### 创建一个新容器并映射指定端口给容器用于网络通讯

```
$ docker run -p 8888:8080 --name tomcat_salah tomcat:7
```