这是一个学习笔记仓库，使用Obsidian软件作为记录。

仓库同步位置：https://github.com/130926C/ObsidianBackUp

---

#### Ubuntu 如何将添加到桌面和软件管理中

参考连接：https://zhuanlan.zhihu.com/p/664186491?utm_id=0

* Step1. 下载`AppImage` 软件（https://obsidian.md/）；
* Step2. 给下载下来的文件重置权限 `chmod 777 Obsidian.AppImage`;
* Step3. 下载一个`png`图标（https://obsidian.md/blog/new-obsidian-icon/）；
* Step4. 将图标和软件移动到一个自定义的文件夹下，以便未来管理；
* Step5. 创建一个名字为 `obsidian.desktop` 的文件并添加以下内容：
```txt
[Desktop Entry]
Name=Obsidian
Exec=/home/gaohao/ThriedPartyApp/Obsidian-1.5.3.AppImage
Terminal=false
Type=Application
Icon=/home/gaohao/ThriedPartyApp/obsidian-icon.png
StartupWMClass=obsidian
X-AppImage-Version=1.5.3
Comment=Obsidian
MimeType=x-scheme-handler/obsidian;
Categories=Office;Productivity;TextEditor;Development;IDE;
StartupNotify=true
```

1.  `Exec` ：软件所在绝对路径；
2.  `Terminal`：是否在打开软件后顺便打开终端；
3. `Icon`：软件图标绝对路径；
4. `StartupNotify`：是否在通知中心添加软件通知；

* Step6. 将 `obsidian.desktop` 文件拷贝一份到下面的位置：
```shell
$ cp obsidian.desktop ~/.local/share/applications/
```
* Step7. 更新桌面软件管理器：
```shell
$ update-desktop-database ~/.local/share/applications/
```

【注意】上面的操作适用于任何 `AppImage` 形式的软件。

---
#### 2024-02-18 Sun 

* 添加了`现代C++教程`文件夹；
* 添加了主目录下的 `ReadMe.md` 文件；
* 添加了`Rust现代实用教程` 文件夹；
* 添加了 `Flutter从入门到高阶` 文件夹；
* 修改了原先 `CMake学习笔记` 文件夹名为 `现代C++ CMake简明教程`，目的是为了和对应的系列视频名保持一致；

---

#### 2024-02-21 Wed

* 添加了`GoogleTest`文件夹；
* 添加了 `Linux零碎笔记` 文件夹；

---

#### 2024-02-22 Thu

* 添加了 `读书笔记` 文件夹；
* 添加了 `读书笔记/现代C++教程` 文件夹；