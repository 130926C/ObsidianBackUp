在Windows10平台下编译指定版本的Eigen3.4并在Open3D项目中引用：

---
## Step1. 安装cmake & make

参考文档：
* [Win10 下的 CMake的安装（详细版）-CSDN博客](https://blog.csdn.net/weixin_49649700/article/details/129072973)
* [Windows安装Make工具(make.exe和mingw)_windows make-CSDN博客](https://blog.csdn.net/weixin_41896770/article/details/131262178)

【注】如果已经安装了过了就跳过这步骤。

### CMake安装

1. 进入CMake官网：[Download CMake](https://cmake.org/download/) 选择 `Windows x64 Installer` 下载：
![images01](images/CMake安装.png)

2. 直接运行下载好的 `msi` 文件：
在安装过程中会询问是否添加到系统Path中，选择是即可。

3. 验证CMake是否安装成功：
```shell
$ cmake --version
```

### make安装

安装了cmake并不代表安装了make，因为会在你想用 `make` 命令的时候报错。

1. 进入make官网 [Make for Windows (sourceforge.net)](https://gnuwin32.sourceforge.net/packages/make.htm) 下载 `Complete package, except sources`"：
![images02](images/make安装.png)

2. 下载完成中直接运行 `exe` 文件进行安装，默认安装位置如下：
```txt
C:\Program Files (x86)\GnuWin32\bin
```

3. 将上述路径添加到系统环境变量中后验证：
```shell
$ make -version
```

---
## Step2. 下载Eigen3源码并编译

参考文档：
* [通过CMake使用Eigen3 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/361969822)

1. 进入GitLab仓库下载Release版本 `源代码.zip`：
![images03](images/Eigen3官网.png)

2. 下载后将其解压到对应的位置：
```txt
C:\Users\Lucks\Downloads\3rdlibrary\eigen3
```

3. 执行创建一个用来存放编译过程的文件夹，并执行编译：
```shell
$ mkdir build
$ cd build
$ cmake .. DCMAKE_INSTALL_PREFIX="C:/Users/Lucks/Downloads/3rdlibrary/install/eigen3"
```
* `C:/Users/Lucks/Downloads/3rdlibrary/install/eigen3`：放置install的文件夹，这里一定要是 **绝对路径**，否则在项目中引用时会报错；

4. 执行完上面的命令后会有提示指示如何执行 `install` 命令：
【注意】：很多博客将这一步遗漏了，会导致install文件中没有任何东西。
```shell
 Sparse lib flags:

-- ************************************************************
--
-- Configured Eigen 3.4.0
--
-- Available targets (use: cmake --build . --target TARGET):
-- ---------+--------------------------------------------------------------
-- Target   |   Description
-- ---------+--------------------------------------------------------------
-- install  | Install Eigen. Headers will be installed to:
--          |     <CMAKE_INSTALL_PREFIX>/<INCLUDE_INSTALL_DIR>
--          |   Using the following values:
--          |     CMAKE_INSTALL_PREFIX: C:/Users/Lucks/Downloads/3rdlibrary/install/eigen3
--          |     INCLUDE_INSTALL_DIR:  include/eigen3
--          |   Change the install location of Eigen headers using:
--          |     cmake . -DCMAKE_INSTALL_PREFIX=yourprefix
--          |   Or:
--          |     cmake . -DINCLUDE_INSTALL_DIR=yourdir
-- doc      | Generate the API documentation, requires Doxygen & LaTeX
-- check    | Build and run the unit-tests. Read this page:
--          |   http://eigen.tuxfamily.org/index.php?title=Tests
-- blas     | Build BLAS library (not the same thing as Eigen)
-- uninstall| Remove files installed by the install target
-- ---------+--------------------------------------------------------------
--
-- Configuring done (31.7s)
-- Generating done (4.3s)
-- Build files have been written to: C:/Users/Lucks/Downloads/3rdlibrary/eigen3/build
(base) PS C:\Users\Lucks\Downloads\3rdlibrary\eigen3\build>
```

上面提示说使用 `use: cmake --build . --target TARGET` 命令来执行想要的动作，其中 `TARGET` 就是下面表格中左侧的内容，这里想要进行 install 就需要执行下面的命令：
```shell
$ cmake --build . --target install
```

----
## Step3. 在项目中引用Eigen3

在步骤2中完成install后可以得到下面的结构：
```shell
(base) PS C:\Users\Lucks\Downloads\3rdlibrary\install\eigen3> tree
	├───include
	└───share
	    └───eigen3
	        └───cmake
```

其中 `share` 目录下的 `eigen3/cmake` 就是我们需要锁定的路径；

填写 `CMakeLists.txt` 文件：
```cmake
cmake_minimum_required(VERSION 3.15)
project(demo)

set(Eigen3_DIR "C:/Users/Lucks/Downloads/3rdlibrary/install/eigen3/share/eigen3/cmake")

find_package(Eigen3 REQUIRED)

if (Eigen3_FOUND)
    message(${EIGEN3_INCLUDE_DIRS})
else()
    message(FATIAL_ERROR ": Eigen3 Not Found")
endif()

include_directories(
    ${Eigen3_INCLUDE_DIRS}
)
add_executable(demo
    src/main.cpp
)
target_link_libraries(demo
    Eigen3::Eigen
    或者
    ${Eigen3_LIBRARIES}
)
```