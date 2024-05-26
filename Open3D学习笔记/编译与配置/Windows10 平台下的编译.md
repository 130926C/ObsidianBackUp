
在Windows10平台下有多种编译方式，这篇文章将逐一介绍。

因为Open3D在编译和运行期间依赖很多第三方的库，主要体现在源码 **.cmake** 文件中一系列的 `ExternalProject_Add` 和 `FetchContent_Declare` 字段中，这两个字段都声明了从远端仓库拉取版本的功能。

因为 Open3D 的版本之间存在一些差异，有时候我们又需要在不同版本之间来回切换，所以我个人会编译多个可用的版本，在项目中只需要指定版本路径即可，我通常喜适合的文件结构如下：

```shell
$ C:\Users\Lucks\Downloads\Open3D

**C:.
├───build         # 编译过程文件夹
│   ├───open3d141                  # 0.14.1 Release 编译过程中生成的文件
│   ├───open3d141_debug            # 0.14.1 Debug 编译过程中生成的文件
├───install       # 安装位置文件夹
│   ├───open3d141                  # 0.14.1 Release 安装的位置
│   ├───open3d141_debug            # 0.14.1 Debug   安装的位置
├───source        # 源码文件夹
│   ├───Open3D141                  # 0.14.1 源码
├───test_demo     # 测试文件夹
│   ├───build                      # 测试demo编译过程文件夹
│   ├───open3d-cmake-find-package  # 官方测试demo源码
```

这个文件夹是用来放所有和Open3D有关的内容，当然你也可以随意放置，只要符合你的使用习惯即可。

----
## 【推荐】下载完整的源码编译

这里已经存放了一个 `v0.14.1` 版本完整的源码，包括了第三方库，下载后就可以直接进行原地编译：
* 百度网盘：`https://pan.baidu.com/s/1TN9_CTnspF_-Ec0-86SmUg?pwd=my1x`
* 链接密码：`my1x`

下载并解压后里面有几个 bat 脚本，感兴趣的话可以看下，但这里介绍如何自己从零开始完成整个编译步骤。

1. 下载后解压文件到上面文件夹结构中的 `source` 并修改文件夹名添加版本信息：
```shell
$ mv Open3D source/Open3D141
$ mkdir build/open3d141 
$ mkdir build/open3d141_debug     # 如果不想要Debug版则不执行这句
$ mkdir install/open3d141
$ mkdir install/open3d141_debug   # 如果不想要Debug版则不执行这句
```  
【注】：这一步不是必要的，但需要留意这个路径，因为后面编译时会用到。

2. 编写一个release版本的执行脚本 `compile_release.bat` 文件：
```shell
cmake -DCMAKE_INSTALL_PREFIX="C:\Users\Lucks\Downloads\Open3D\install\open3d141" -DBUILD_PYTHON_MODULE=OFF -DBUILD_WEBRTC=OFF -DBUILD_EXAMPLES=OFF -S ./source/Open3D141 -B ./build/open3d141
cmake --build ./build/open3d141 --config Release --parallel 16
cmake --build ./build/open3d141 --config Release --target install
```

* `-DCMAKE_INSTALL_PREFIX`：编译后安装的位置；
* `./source/Open3D141`：用来编译的源码位置；
* `./build/open3d141`"：编译过程生成的文件存放位置；
* `--config Release`：配置为Release版本；
* `--target install`：配置install操作；

3. 如果想要使用debug版本则对应的脚本文件 `compile_debug.bat` 如下：
```shell
cmake -DCMAKE_INSTALL_PREFIX="C:\Users\Lucks\Downloads\Open3D\install\open3d141_debug" -DBUILD_PYTHON_MODULE=OFF -DBUILD_WEBRTC=OFF -DBUILD_EXAMPLES=OFF -S ./source/Open3D141 -B ./build/open3d141_debug
cmake --build ./build/open3d141_debug --config Debug --parallel 16
cmake --build ./build/open3d141_debug --config Debug --target install
```

那么此时你所在的目录位置应该如下：
```shell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         5/26/2024   2:43 AM                build
d-----         5/26/2024   2:43 AM                install
d-----         5/25/2024   2:58 AM                source
d-----         5/25/2024  11:57 PM                test_demo
-a----         5/26/2024  12:55 PM            340 compile_debug141.bat
-a----         5/26/2024   2:49 AM            320 compile_release141.bat
```

4. 根据需要执行上面编写好的脚本：
```shell
$ ./compile_release141.bat
或
$ ./compile_debug141.bat
```

编译过程中只要没有出现红色报错就是正常，有时候会卡在某些语句上也不用担心，耐心等待即可。

---
## 官方源码编译

在国内使用GitHub源码编译比较麻烦，即便搭了全局的梯子也不是很容易，其核心在于下载第三方库的过程。

1. 从github中拉取源码并切换到对应的分支：
```shell
$ git clone git@github.com:isl-org/Open3D.git
$ mv open3d source/Open3D141
$ cd source/Open3D141
$ git switch v0.14.1
```

2. 因为CMake无法在不执行编译的情况下查看所有依赖的第三方库列表以及URL链接，但是Open3D的第三方依赖库全都在 `CMakeLists.txt` 和各种 `*.cmake` 文件中，可以通过查找下面路径知道某个依赖库的下载链接与版本：

```shell
$ C:\Users\Lucks\Downloads\Open3D\source\Open3D141\3rdparty
```

这个路径中有很多小的文件夹，假设现在需要查看第三方库 `assimp` 的版本与下载链接信息，就打开 `Open3D141\3rdparty\assimp\assimp.cmake` 文件即可，并且关注文件中的 `ExternalProject_Add` 和 `FetchContent_Declare` 字段：：

```txt
ExternalProject_Add(
    ext_assimp
    PREFIX assimp
    URL https://github.com/assimp/assimp/archive/refs/tags/v5.0.1.tar.gz # Jan 2020
    URL_HASH SHA256=11310ec1f2ad2cd46b95ba88faca8f7aaa1efe9aa12605c55e3de2b977b3dbfc
    DOWNLOAD_DIR "${OPEN3D_THIRD_PARTY_DOWNLOAD_DIR}/assimp"
    UPDATE_COMMAND ""
    PATCH_COMMAND ${CMAKE_COMMAND} -E copy ${ASSIMP_PATCH_FILES} <SOURCE_DIR>/code/Obj
    CMAKE_ARGS
        -DBUILD_SHARED_LIBS=OFF
        -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR>
        -DASSIMP_NO_EXPORT=ON
        -DASSIMP_BUILD_ASSIMP_TOOLS=OFF
        -DASSIMP_BUILD_TESTS=OFF
        -DASSIMP_INSTALL_PDB=OFF
        -DASSIMP_BUILD_ZLIB=ON
        -DHUNTER_ENABLED=OFF # Renamed to "ASSIMP_HUNTER_ENABLED" in newer assimp.
        -DCMAKE_DEBUG_POSTFIX=
        ${ExternalProject_CMAKE_ARGS_hidden}
    BUILD_BYPRODUCTS
        <INSTALL_DIR>/lib/${CMAKE_STATIC_LIBRARY_PREFIX}${lib_name}${CMAKE_STATIC_LIBRARY_SUFFIX}
        <INSTALL_DIR>/lib/${CMAKE_STATIC_LIBRARY_PREFIX}IrrXML${CMAKE_STATIC_LIBRARY_SUFFIX}
)
```

* `PREFIX assimp`：依赖的第三方库名为 `assimp`；
* `URL` ：对应的下载链接；
* `DOWNLOAD_DIR`：下载后的文件夹保存位置；

知道这些信息后就可以手动去下载这个库的资源，可以直接拷贝URL链接从浏览器中打开，也可以使用下面的命令：
```shell
$ wget -O v5.0.1.tar.gz https://github.com/assimp/assimp/archive/refs/tags/v5.0.1.tar.gz
```

下载好了这个文件后还需要将文件移动到对应的位置，如果对应位置文件夹不存在就直接创建：
```shell
$ mkdir source\Open3D141\3rdparty_downloads\assimp
$ mv v5.0.1.tar.gz source\Open3D141\3rdparty_downloads\assimp
```

【注】：这部分内容存在以下几种特殊情况：

* 下载链接的文件名没有含义，如 `filament` 库下载完成后需要手动解压：
```txt
ExternalProject_Add(
    ext_filament
    PREFIX filament
    URL https://github.com/isl-org/filament/archive/d1d873d27f43ba0cee1674a555cc0f18daac3008.tar.gz
    URL_HASH SHA256=00c3f41af0fcfb2df904e1f77934f2678d943ddac5eb889788a5e22590e497bd
    DOWNLOAD_DIR "${OPEN3D_THIRD_PARTY_DOWNLOAD_DIR}/filament"
    UPDATE_COMMAND ""
    ...
)
```
* 下载链接中存在本地变量，如 `ippicv` 库就需要寻找上文中的命令将其替换掉：
```txt
ExternalProject_Add(ext_ippicv
    PREFIX ippicv
    URL https://raw.githubusercontent.com/opencv/opencv_3rdparty/${IPPICV_COMMIT}/ippicv/${OPENCV_ICV_NAME}
    URL_HASH SHA256=${OPENCV_ICV_HASH}
    DOWNLOAD_DIR "${OPEN3D_THIRD_PARTY_DOWNLOAD_DIR}/ippicv"
    UPDATE_COMMAND ""
    ...
)
```

3. 在将所有的第三方依赖库下载完成后就可以同上面一样写一个编译脚本并执行了：
```shell
$ ./compile_release141.bat
或
$ ./compile_debug141.bat
```

----
## 测试编译库

Open3D官方提供了一个包用来测试编译是否完成，因为在编译过程中会有很多输出，终端会覆盖掉前面的部分包括各种报错信息，所以他们提供了一个库用来测试编译结果。

1. 从GitHub中拉取测试库：
```shell
$ git clone git@github.com:isl-org/open3d-cmake-find-package.git
```

2. 编写一个运行脚本 `compile.bat`：
```bat
cmake -DOpen3D_DIR="C:/Users/Lucks/Downloads/Open3D/install/open3d141/CMake" ^
-S ./open3d-cmake-find-package . -B ./build
cmake --build ./build --config Release --target ALL_BUILD
```

* `-DOpen3D_DIR` ：这个就是Open3D编译好的安装路径，该路径下必须能看到 `Open3DConfig.cmake` 这个文件，因为有些分支编译完后没有 `CMake` 这个文件夹，而是将里面的文件都放在了编译后的同级问价夹下；

3. 运行编译脚本：
```shell
$ ./compile.bat
```

4. 运行测试案例：
```shell
$ ./test_demo/build/Release/Draw.exe
```
只要运行结构没有报错则说明完整编译。