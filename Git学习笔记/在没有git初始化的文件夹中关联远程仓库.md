
在一个没有进行git初始化的文件夹中添加远程仓库链接，并以一个全新的分支进行维护。

1. 初始化git仓库
```shell
$ git init
$ git add .
$ git commit -m "new branch"
```
2. 创建一个新的分支
```shell
$ git checkout -b new_dev
```
3. 添加远程仓库链接
```shell
$ git remote add origin https://...
```
4. 推送到远程仓库
```shell
$ git push -u origin new_dev
```

