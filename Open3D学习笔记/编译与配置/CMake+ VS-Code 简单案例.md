
在Windows平台下使用Open3D是一件比较吃力的事情 ，特别是不使用Visual Stduio的情况下需要自己手动配置很多东西，网上已经有很多关于使用 Visual Stduio的教程了，这里就只介绍如何用 Visual Studio Code 和 CMake 编译一个简单的案例；

---
## 写一个简单的demo

1. 在任意位置创建一个项目文件夹，然后写一个简单的demo：
```shell
(base) PS C:\Users\Lucks\Desktop\open3dDemo> ls

    Directory: C:\Users\Lucks\Desktop\open3dDemo

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         5/26/2024  12:41 AM                .vscode
d-----         5/26/2024   1:35 PM                src
-a----         5/26/2024   1:45 PM            884 CMakeLists.txt
-a----         5/26/2024   1:42 PM            235 compile.bat
```

2. 为了能让 Visual Studio Code 能够生成代码提示，需要配置一下 `c_cpp_properties.json` 文件，主要是在 `includePath` 字段中添加下头文件路径：
```json
"includePath": [
	"${workspaceFolder}/**",
	"C:/Users/Lucks/Downloads/Open3D/install/open3d141/include",
	"C:/Users/Lucks/Downloads/Open3D/install/open3d141/include/open3d/3rdparty"
],
```
* `"C:/Users/Lucks/Downloads/Open3D/install/open3d141/include"`：install文件夹下的include路径；

3. 编写 `src/main.cpp` 文件：
```cpp
#include <iostream>

#include "open3d/Open3D.h"

int main() {
    using namespace open3d::geometry;
    auto sphere = TriangleMesh::CreateSphere(1.0,4);
    sphere->ComputeVertexNormals();
    sphere->PaintUniformColor({1, 0.706, 0});

    std::cout << sphere->vertices_.size() << " vertices\n";
    std::cout<<sphere->triangles_.size()<<" triangles\n";

    open3d::visualization::DrawGeometries({sphere});
}
```

4. 【关键】编写 `CMakeLists.txt` 文件：
```cmake
cmake_minimum_required(VERSION 3.19.2)
project(demo)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")
set(CMAKE_BUILD_TYPE "Release")                                             # 设置编译模式为Release
set(Open3D_DIR "C:/Users/Lucks/Downloads/Open3D/install/open3d141/CMake")   # 设置Open3D文件路径
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")     # 根据输入设置为Release模式

find_package(Open3D REQUIRED)

message(${Open3D_INCLUDE_DIRS})
include_directories(
  ${Open3D_INCLUDE_DIRS}
  ${PROJECT_SOURCE_DIR}
)

add_executable(demo
  src/main.cpp
)
target_link_libraries(demo PRIVATE
    Open3D::Open3D
)
```

* `set(CMAKE_BUILD_TYPE "Release")`：设置编译模式为 Release 版，如果希望使用debug版也可以；
* `set(Open3D_DIR ...)` ：设置Open3D文件路径，这个路径下必须包含 `Open3DConfig.cmake` 等文件；
* `option(STATIC_WINDOWS_RUNTIME)` ：设置静态库；
	* `MultiThreaded$<$<CONFIG:Debug>:Debug>`  如果当前是Debug模式编译的则返回Debug，否则返回空，
	* 参考链接：[CMake设置MSVC运行库_cmake 中 msvc-CSDN博客](https://blog.csdn.net/b1049112625/article/details/136566721)；
	* 参考链接：[2019-10-24-cmake命令行_cmake --target all-CSDN博客](https://blog.csdn.net/knowledgebao/article/details/113339079)；

5. 执行编译操作：
```shell
$ cmake -B build
$ cmake --build build --config Release --target ALL_BUILD
```

【注】：这里的 `--config` 参数一定要带上 `Release` 信息，否则会出现以下报错：

```shell
Open3D.lib(Geometry3D.obj) : error LNK2038: mismatch detected for '_ITERATOR_DEBUG_LEVEL': value '0' doesn't match value '2' in main.obj [C:\Us
ers\Lucks\Desktop\open3dDemo\build\demo.vcxproj]
```

意思就是链接时将一个 Release 库链接到了 Debug 库上。

6. 运行程序：
```shell
$ ./build/Release/demo.exe
```

---

## 数据集

下面的链接提供了 Open3D 的官方数据集，主要在 **Open3D Data** 的部分中查找。
 
[Releases · isl-org/open3d_downloads (github.com)](https://github.com/isl-org/open3d_downloads/releases?page=2)

