
参考文档：[QT开发 2024最新版本优雅的使用vscode开发QT - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/677592053)

---

## Step1. 下载VS Code相关插件

Qt需要在VS Code中的下列插件支持，可以直接在插件商店中查找：
* C/C++；
* CMake；
* CMake Tools；
* Qt Configure；
* Qt Tools；

## Step2. 配置编译器路径

打开VS Code之后在设置里面搜索 `@ext:vector-wlc.qtconfigure` ：

![images09](images/Qt配置vscode编译器路径.png)

填写下面内容：
* Qt Configure: Mingw Path ：编译器路径地址；
* Qt Configure: Qt Dir：Qt的安装路径；

## Step3. 创建项目

* 按住组合键 `Ctrl` + `Shift` + `P` 键，然后输入 `QtConfigure: New Project`，以此输入项目名、选择编译器、CMake；
*  按住组合键 `Ctrl` + `Shift` + `P` 键，然后输入 `QtConfigure: Set Qt Dir` 选择安装qt的位置；
* 按住组合键 `Ctrl` + `Shift` + `P` 键，然后输入 `CMake: Configure`；
* 按住组合键 `Ctrl` + `Shift` + `P` 键，然后输入 `CMake: Select a Kit` 选择编译器；


## Step4. 编译并运行项目

这个时候就有两种方式编译和运行项目了，因为上面的四步操作已经将cmake配置好了；

* 方式一：使用传统cmake命令方式
```shell
$ cmake -B build
$ cmake --build build
```

* 方式二：直接点击VS Code下方工具栏中的三角箭头