
这个 `ppconsul` 库是用来与 `consul` 进行通讯的工具库，这里写了一个比较复杂的例子用来对该库进行引用，定义项目名为 `ConsulTest`，并且将所有第三方库放到 `third-party` 文件夹下：

其中 `ppconsul` 库需要对源码中的 `CMakeLists` 文件进行修改，否则会出现编译错误：

* 位置一：
```cmake
# 将这一整块注释掉，禁止去找git，大约在17行
if(GIT_FOUND)
    execute_process(
            COMMAND ${GIT_EXECUTABLE} describe --match "v[0-9]*" --abbrev=0 --tags HEAD
            WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}"
            OUTPUT_VARIABLE DESCRIBE_BUILD
            OUTPUT_STRIP_TRAILING_WHITESPACE
    )
    string(REGEX REPLACE "^v" "" VERSION ${DESCRIBE_BUILD})
else()
    set(VERSION "0.0.0")
endif()
```

* 位置二：
```shell
# 将 ON 修改为 OFF，不去生成TESTS文件
option(BUILD_TESTS "Flag to use to build test or not" ON)
if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()
```


修改完成后的目录结构如下：
```shell
(base) ~/Desktop/ConsulTest $ tree -L 2
.
├── CMakeLists.txt
├── build
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   ├── Makefile
│   ├── MyProject
│   ├── cmake_install.cmake
│   ├── output
│   └── third_party
├── src
│   └── main.cpp
└── third_party
    ├── jsoncpp
    ├── ppconsul
    └── yaml-cpp
```

完整的 `CMakeLists.txt` 文件：
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

find_package(Boost REQUIRED COMPONENTS system filesystem)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# boost
include_directories(${Boost_INCLUDE_DIRS})
# jsoncpp
add_subdirectory(third_party/jsoncpp)
# yaml-cpp
add_subdirectory(third_party/yaml-cpp)
# ppconsul
add_subdirectory(third_party/ppconsul)
include_directories(third_party/ppconsul/include)

# 添加执行文件
add_executable(${PROJECT_NAME}
 src/main.cpp
)
target_link_libraries(${PROJECT_NAME}
    ${Boost_LIBRARIES}
    jsoncpp_lib
    yaml-cpp
    ppconsul
    curl
)
```
