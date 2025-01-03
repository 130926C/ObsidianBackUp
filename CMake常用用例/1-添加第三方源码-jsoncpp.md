该文档介绍如何将从github上拉下来的源码添加进CMakeLists文件中并编译。

---

假设从`github`中拉取了`jsoncpp`的[源码](https://github.com/open-source-parsers/jsoncpp)，合并安排了如下文件结构：

```shell
.
├── CMakeLists.txt
├── include
│   └── common_utils.h
├── libs
│   └── jsoncpp
└── src
    └── main.cpp
```

其中第三方来源的源码都存放在 `libs` 库中，`CMakeLists.txt` 文件编写如下：

```cmake
cmake_minimum_required(VERSION 3.0)
project(MyProject)

# [可选] 如果想以release方式进行编译，则添加下面的语句
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
endif()

# 导入 jsoncpp 源码目录
add_subdirectory(libs/jsoncpp)

# 添加包含路径（可选，取决于 jsoncpp 是否已在 add_subdirectory 中自动添加了 include 目录）
include_directories(libs/jsoncpp/include)

# 添加你的可执行文件
add_executable(MyExecutable src/main.cpp)

# 链接 jsoncpp 库
target_link_libraries(MyExecutable jsoncpp_lib)

```

然后执行下面的语句：
```shell
$ cmake -B build
$ cmake --build build
```

因为这里是将jsoncpp一起编译了，额外编译出来的第三方库会放在 `build/libs` 这个目录下。

### 如何找到正确的库名

上面有一条关于怎样知道 `target_link_libraries` 中添加什么动态连接库的部分，这个通常是需要去第三方库的 `CMakeLists.txt` 文件中查找，根据 ChatGPT 提供的建议，可以关注以下几个关键字：

* `add_library(jsoncpp_lib STATIC ...)`：这里定义了库名为 `jsoncpp_lib`；有时库会使用`ALIAS` 将导出的库重命名为其他名字，如：`add_library(<name> ALIAS <actual_target>)` 来给目标库添加别名，或使用 `export` 和 `install` 命令导出目标名；
* `ReadMe.md`：有些库会在 ReadMd.md 文档中高速使用者如何正确添加他们的库，通常会这么写：`target_link_libraries(MyExecutable jsoncpp_lib)`；
* `cmake-gui`：使用 cmake-gui 工具在构建项目时可以获取项目名，生成的库名通常会出现在 `LIBRARY_OUTPUT_NAME` 或 `BUILD_SHARED_LIBS` 相关设置中，这两个字段也可以用来判断导出的库名；
* 尝试使用通用约定俗称的名字：规范的库通常可以直接使用 `xxx_lib` 作为库名，或者 `libxxx`，可以尝试使用这种约定俗称的名字，编译失败的话就换一个再试；

【注意】：使用源码编译与直接 `apt-install libjsoncpp` 最后链接的库名可能存在区别，因为 `install` 方式是直接安装编译好的库，而源码是需要查找的。

---

### 复杂的实操

这个例子中添加了 `jsoncpp`、`spdlog`、`Eigne 3.4.0` 三个常用的库，项目组织结构如下：
```shell
$ tree -L 2
.
├── CMakeLists.txt
├── build
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   ├── Makefile
│   ├── MyExecutable
│   ├── cmake_install.cmake
│   └── libs
├── include
│   └── common_utils.h
├── libs
│   ├── eigen-3.4.0
│   ├── jsoncpp
│   └── spdlog
└── src
    └── main.cpp
```

首先应该去查看则三个库导出的库名是什么：

#### jsoncpp

在这个库的源码中找不到 `add_library` 这个字段，并且 `ReadMe.md` 文件中也没有 `target_link_libraries` 字段，但是发现有下面三句话：
```cmake
option(BUILD_SHARED_LIBS "Build jsoncpp_lib as a shared library." ON)
option(BUILD_STATIC_LIBS "Build jsoncpp_lib as a static library." ON)
option(BUILD_OBJECT_LIBS "Build jsoncpp_lib as a object library." ON)
```
这三个选项用于控制库的构建方式（共享库、静态库、对象库），那么说明可以使用 `jsoncpp_lib` 作为导出库的链接库名。

#### spdlog

这个库的源码中有下面几个字段，说明其提供了多种方式进行编译和引用：
```cmake
add_library(spdlog::spdlog ALIAS spdlog)

add_library(spdlog_header_only INTERFACE)

add_library(spdlog::spdlog_header_only ALIAS spdlog_header_only)
```

这个时候就需要仔细去阅读源码文件，通过查找可以发现 `SPDLOG_BUILD_SHARED` 选项是关闭的，说明并没有编译动态连接库：
```cmake
# build shared option
option(SPDLOG_BUILD_SHARED "Build shared library" OFF)
```

还发现文件将 `spdlog::spdlog` 重命令为 `spdlog` ，那么就可以直接使用其作为库名。

如果想使用纯头文件形式，那么就使用 `spdlog_header_only` 作为库名。

#### eigen

这里选择了 3.4.0 版本作为源码版本，能够发现存在以下两个字段：
```cmake
add_library (eigen INTERFACE)

add_library (Eigen3::Eigen ALIAS eigen)
```

那么就直接使用 `eigen` 作为库名即可。

#### CMakeLists 文件

那么添加了上面三个库的 `CMakeLists.txt` 文件内容如下：
```cmake
cmake_minimum_required(VERSION 3.0)
project(MyProject)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
endif()

# 导入第三方库的源码目录
add_subdirectory(libs/jsoncpp)
add_subdirectory(libs/spdlog)
add_subdirectory(libs/eigen-3.4.0)

# 添加头文件包含路径
include_directories(${CMAKE_SOURCE_DIR}/libs/jsoncpp/include)
include_directories(${CMAKE_SOURCE_DIR}/libs/spdlog/include)
include_directories(${CMAKE_SOURCE_DIR}/libs/eigen-3.4.0)

# 添加你的可执行文件
add_executable(MyExecutable src/main.cpp)

# 链接第三方库
target_link_libraries(MyExecutable 
  jsoncpp_lib
  spdlog
  eigen
)
```

编译与运行：
```shell
$ cmake -B build
$ cmake --build build

$ ./build/MyProject
```

