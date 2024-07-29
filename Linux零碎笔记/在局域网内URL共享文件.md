## 在局域网内将文件制作成URL连接共享

这篇文章介绍了如何在局域网内将文件制作成URL连接以方便其他设备拉取文件，涉及到资源服务器Server和客户端Client，以及拉取命令 wget。

---

### 资源服务器 Server

1. 进入需要共享文件的目录
```shell
$ cd Desktop/XianData/
```

2. 开启python的http服务为8000的端口
```shell
$ ~/Desktop/XianData $ python3 -m http.server 8000
Serving HTTP on :: port 8000 (http://[::]:8000/) ...
```

【注意】：这里必须进入共享文件夹目录中才可以，否则搜索不到文件；端口号可以任意指定，但需要避免端口号被占用；

---

### 客户端 Client

根据协商好的端口号拉取文件：

```shell
$ wget http://127.0.0.1:8000/task_config.json
```

【注意】：如果遇到关于网关的502报错：

```shell
--2024-07-25 10:33:31--  http://127.0.0.1:8000/task_config.json
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 502 Bad Gateway
2024-07-25 10:33:31 ERROR 502: Bad Gateway.
```

需要临时禁用代理：
```shell
$ unset http_proxy
$ unset https_proxy
```

----

### 检查有哪些端口号被占用了

Linux下有多种方式查看已经被占用的网络端口：

```shell
$ ~/Desktop/XianData $ sudo lsof -i -P -n | grep LISTEN
launchd       1           root    7u  IPv6 0xebae30a8afa65d1b      0t0    TCP *:5900 (LISTEN)
launchd       1           root    8u  IPv6 0xebae30a8afa6551b      0t0    TCP *:22 (LISTEN)
launchd       1           root    9u  IPv4 0xebae30a3e316ea13      0t0    TCP *:22 (LISTEN)
```