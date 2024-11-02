
从远程仓库拉取指定分支：
```shell
$ git clone -b <branch名> <仓库URL>
```
例如：
```shell
$ git clone -b dev git@github.com:Kitware/CMake.git
```

---

从远程仓库拉取指定tag：
```shell
$ git clone --branch=<tag名> --single-branch <仓库URL>
```
例如：
```shell
$ git clone --brance=v3.14.0 --single-brance git@github.com:Kitware/CMake.git
```