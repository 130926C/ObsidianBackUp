
---

#### git submodule

参考链接：https://blog.csdn.net/qq_38880380/article/details/123288706

添加某个仓库的指定分支

```shell
$  git submodule add -b branch_name repo_url
```

从远端拉取仓库（一次性拉取）

```shell
$  git clone <repository> --recursive
```

从远端拉取仓库（分步操作）
```shell
$  git clone <repository>
$  git submodule init                 # 初始化子模块
$  git submodule update               # 更新子模块
$  git submodule foreach git pull     # 拉取所有子模块
```

