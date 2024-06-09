
参考链接：
* [Qt5.15.2安装（详细教程） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/697911596)

----

为什么一定要安装5.15.2这个版本呢，如果你想要Qt 6的话直接下载就可以，但很多项目或公司目前仍然使用的是Qt 5这个版，而官网能够提供的长期支持版目前就到了 5.15.2 这个版本号上；另一方面，**只有5.15.X** 以后的本版（包括Qt6）才可以兼容cmake工具，其他版本只能用qmack进行构建，Qt 官方已经在Qt 6上放弃了qmake，所以无论从通用角度还是未来出发都最好以这个版本作为切入点。

因为Qt官方现在在主推Qt6+在线下载，所以会发现无论是官网还是镜像网站都找不到原来的离线下载包 .exe 文件，那么就只能使用下面的方法在线下载：

1. 进入清华镜像网站 [Index of /qt/official_releases/online_installers/ | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/qt/official_releases/online_installers/)
![images](images/清华镜像.png)
进去后点击 `qt-unified-windows-x64-online.exe` 将官方提供的下载器下载下来。

2. 进入下载的文件夹使用下面的命令启动，这样可以将下载源定位到国内镜像，下载速度能够显著提升
```shell
.\qt-unified-windows-x64-online.exe --mirror https://mirrors.ustc.edu.cn/qtproject
```

3. 在账户登录界面输入自己的账号密码：
![images02](images/Qt安装登录界面.png)

4. 在使用许可证界面勾选下面两个选择框，因为我这个账号是在读研时注册的，所以默认不能选择下面的勾选框；
* I have read and agree to the terms and conditions of using Open Source Qt；
* I'm an individual and do not use Qt for any company；
![images03](images/Qt安装许可界面.png)

5. 在发送日志文件界面选择不发送：
![images04](images/Qt安装发送界面.png)

6. 点击 `Next` 按钮后会弹出安装器默认的一个安装界面，在这个界面中选择 `Custom Installation`
![images05](images/Qt安装默认界面.png)

7. 点击`Next`后会弹出下面的界面，勾选 `Archive`后点击  `Filter` 按钮：
![images06](images/Qt安装选择界面.png)

8. 点击后会弹出新的版本选择界面，选择 `Qt5.15.2`后取消勾选 `Qt Design Studio 4.5.0`：
![images07](images/Qt安装指定版本.png)

【注意】：这里需要取消勾选 `Qt Design Stduio 4.5.0` 这个选项，因为我在尝试的时候发现他们已经移除了该工具，会导致下载被中断，如果确实需要的话可以勾选 `Qt Design Studio 4.1.0 LTS` 这个下载老版本。


9. 在安装完成后需要配置系统环境变量，这一步其实也可以省略，但这样后面每个项目都需要手动指定一下比较麻烦：
![images08](images/Qt配置环境变量.png)
主要是将安装目录下这几个文件夹添加进去：
* C:\Qt5.15.2\5.15.2\mingw81_64\bin
* C:\Qt5.15.2\5.15.2\mingw81_64
* C:\Qt5.15.2\Tools\CMake_64\bin
* C:\Qt5.15.2\Tools\mingw810_64\bin

这样就算配置完成Qt在Windows平台上的环境了，下一篇文章将介绍如何在Vscode上使用Qt写代码并编译运行。