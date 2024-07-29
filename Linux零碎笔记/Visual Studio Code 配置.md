
进入 Visual Studio Code官网下载期望的版本 https://code.visualstudio.com/updates/v1_91 

这里推荐如果是 Ubuntu 的话选择 `November 2023` 版本，因为最新的版本需要更新 `libc6` ，否则不能直接安装。

有关服务器端配置的原文连接： https://blog.csdn.net/qq_33909269/article/details/136676954

----
### 服务器安装 

无GPU的服务器安装 VS code和有GPU的一样，下载好包后直接安装即可：

```
$ sudo dpkj -i code_xxx
```

---
### 配置无密码远程

原文连接 https://blog.csdn.net/qq_33909269/article/details/136676954 

#### 本地机部分

首先要获取 RSA 密钥，查看自己根目录下是否已经有了公钥 `id_rsa.pub` ：
```
$ cd /home/gaohao/.ssh
```

如果已经存在 `id_rsa.pub` 文件则不用重新生成，打开后直接复制里面的内容，否则使用下面的命令创建公钥：
```
$ ssh-keygen -t rsa
```

#### 服务器部分

同本地机部分一样，首先检查服务器是否已经存在 `id_rsa.pub` 公钥，如果不存在则创建一个。

然后进入服务器的 `.ssh` 文件夹检查是否有 `authorized_keys` 文件，这个文件是安装完 VS Code后自动生成的，初始时间是一个空文件，如果没有则手动创建一个。

```shell
$ cd /home/root/.ssh && ll
total 24
drwx------  2 root root 4096 Jul 25 12:30 ./
drwx------ 20 root root 4096 Jul 25 12:30 ../
-rw-------  1 root root  570 Jul 25 12:30 authorized_keys
-rw-------  1 root root 2362 Mar  3 10:17 id_rsa
-rw-r--r--  1 root root  549 Mar  9 16:12 id_rsa.pub
-rw-r--r--  1 root root  444 Mar  3 10:15 known_hosts
```

然后将本地机中的公钥 `id_rsa.pub` 复制到这个文件中，如果已经存在了则另启一行追加即可。

