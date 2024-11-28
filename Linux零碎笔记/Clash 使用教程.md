
原文连接：https://www.zhihu.com/question/330126342/answer/3258476011

---
### Step1. 下载一个可用的clash文件

由于clash作者删库跑路，目前没有官方库可用，在网上找到了一个可以下载的链接：
https://github.com/FB208/clash-linux-amd64-v1.2.0/blob/master/clash-linux-amd64

为了防止这个链接也被和谐，我还在飞书、本地笔记的Append Files中保存了 Windows、MacOS （M 芯片）、Linux的备份，如果实在没办法就用这些老版本软件。

或者在当前仓库中的 `sources` 文件夹中找到对应的版本：
* Clash for Windows.app  -  MacOS；
* clash-linux-amd64：Ubuntu18.04；

---
### Step2. 配置clash

给clash文件权限并修改名字，如果下载下来的是压缩包，则根据实际情况进行解压：
```shell
$ mkdir Clash
$ chmod +x clash-linux-amd64
$ mv clash-linux-amd64 clash
```

将Append Files中的 `Country.mmdb` 文件移动到Clash文件夹中：
```shell
$ cp Country.mmdb Clash
```

【注】直接运行clash也可以生成这个文件，但这里存在一个鸡生蛋蛋生鸡的问题，下载这个文件需要翻墙，推荐是直接使用现成的，等这个文件下载好之后立刻停止软件，因为还要下载订阅链接。
```shell
$ ./clash
```

---
### Step3. 下载订阅链接

这一步就需要连接到外网，或者提供一个可用的订阅链接：
```shell
$ wget -O config.yaml clash_curl
```

----
### Step4. 进去网页端

使用浏览器进入 http://clash.razord.top/#/proxies 选择想要使用的代理。

---
### Step5. 设置开机自启

创建服务文件：
```shell
$ sudo vim /etc/systemd/system/clash.service
```

编辑服务文件 `clash.service` ：
```txt
[Unit]
Description=Clash Daemon
Documentation=https://github.com/Dreamacro/clash
After=network.target
 
[Service]
User=root
Type=simple
ExecStart=/home/user/Clash/clash -d /home/user/Clash/
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target
```

重新启动服务
```shell
$ sudo systemctrl enable clash.service
$ sudo systemctrl start clash.service
```