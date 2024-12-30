
该文章介绍了如何使用cmake实现release与debug版本编译，主要有两种方式：cmake命令执行、cmakelists文件指定；

示范工程文件结构如下：
```shell
(base) root@iZ2ze5he4cbhwoxibavbv4Z:~/Desktop/cmake_exercise# tree
.
├── CMakeLists.txt
├── debug             # 存放debug编译结果
├── release           # 存放release编译结果
├── build             # 存放cmakelist配置编译结果
└── src
    └── demo.cpp

3 directories, 2 files
```

`CMakeLists.txt` 文件内容如下：
```cmake
cmake_minimum_required(VERSION 3.0)
project(demo)

add_executable(${PROJECT_NAME}
    src/demo.cpp
)
```


----
## 命令方式编译

使用命令行在编译时制定 Debug 或 Release 版本：

### Release 编译
命令 `cmake -DCMAKE_BUILD_TYPE=Release` 配置 `Release` 编译：
```shell
$ cd release
$ cmake -DCMAKE_BUILD_TYPE=Release ..

-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /root/Desktop/cmake_exercise/release
```
命令后面的 `..` 是用来指定项目工程 CMakeLists.txt 所在位置。

执行编译动作：
```shell
$ cmake --build .

[ 50%] Building CXX object CMakeFiles/demo.dir/src/demo.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
```


### Debug 编译
命令 `cmake -DCMAKE_BUILD_TYPE=Debug` 配置 `Debug` 编译：
```shell
$ cd debug
$ cmake -DCMAKE_BUILD_TYPE=Debug ..

-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /root/Desktop/cmake_exercise/debug
```
执行编译动作：
```shell
$ cmake --build .

[ 50%] Building CXX object CMakeFiles/demo.dir/src/demo.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
```


---
## CMakeLists 文件配置方式

```cmake
cmake_minimum_required(VERSION 3.0)
project(demo)

# 如果没有指定编译方式，则使用 Release 方式
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Build type" FORCE)
endif()

# 打印一下编译类型
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

add_executable(${PROJECT_NAME}
    src/demo.cpp
)
```

配置编译动作：
```shell
$ cmake -B build

-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Build type:Release
-- Configuring done
-- Generating done
-- Build files have been written to: /root/Desktop/cmake_exercise/build
```
执行编译动作：
```shell
$ cmake --build build

[ 50%] Building CXX object CMakeFiles/demo.dir/src/demo.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
```

---
## 查看编译生成的文件类型

使用 `readelf` 命令查看编译生成的 `ELF` 文件中是否包含调试信息或优化设置从而判断是 release 还是 debug：
```shell
$ readelf -S demo | grep debug

  [26] .debug_aranges    PROGBITS         0000000000000000  00001039
  [27] .debug_info       PROGBITS         0000000000000000  00001069
  [28] .debug_abbrev     PROGBITS         0000000000000000  000038d4
  [29] .debug_line       PROGBITS         0000000000000000  00003e61
  [30] .debug_str        PROGBITS         0000000000000000  00004256
```

【注意】：命令如果使用 `release` 作为后缀则不会输出任何信息。

1. 如果生成的可执行文件是release版，则不会输出任何信息，除非手动移除了调试符号；
2. 如果输出中有 `.debug_*` 段，则通常是 Debug 版本；
