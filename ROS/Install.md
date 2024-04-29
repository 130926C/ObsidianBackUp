这个文档介绍了如何使用ROS Install更新包。

---
## Step1. 配置当前为install
在配置之前如果已经编译好了，则需要删除编译好的包，因为install需要重新编译。
```shell
$ catkin config --install
```

## Step2. 执行编译
```shell
$ catkin build
```

## Step3. source
```shell
$ source install/setup.bash
```

【注意】：在追加install之前仍然需要编写 `add_library`，`add_executable` 这些

---
## CMakeList文件示例

如果不包含插件的话，使用下面方式添加：
```cmake
install(TARGETS ChassisMiddleWareImpl
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY config DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY cfg DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```

如果包含插件的话，使用下面方式：
```cmake
install(TARGETS ouka450bollarplugin 
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(TARGETS bollard_offline_test
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(FILES Ouka450Plugin.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```

如果多一个节点就需要增加一份
```cmake
install(TARGETS nav_unint_test
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
```

【注意】：`install(TARGETS nav_unint_test` 后面跟的是节点名或者插件名。

---
### TARGETS
如果项目要求某个节点，则需要添加以下内容：
```cmake
install(TARGETS 节点名
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
```

如项目需要 `dg3dnavigationplugin` 和 `nav_unint_test` 这两个节点/运行库：
```cmake
install(TARGETS dg3dnavigationplugin
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(TARGETS nav_unint_test
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
```

---
### DIRECTORY
如果项目要求某个文件夹，则需要添加以下内容：
```cmake
install(DIRECTORY folder_name DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```

如项目需要launch、config这两个文件夹，则这样：
```cmake
install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY config DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```

【注】：上面的文件夹名可以是任意的，只要存在并且依赖就行。

如果需要将头文件install进去方便他人使用，则添加以下内容：
```cmake
install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})
```

【注】：如果不存在launch文件夹，则不能写，否则编译报错。

---

### FILES
如果项目要求某个单独的文件，则需要添加以下内容：
```cmake
install(FILES file_name
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```

如项目需要插件DG3DNavPlugin.xml文件：
```cmake
install(FILES DG3DNavPlugin.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```

如果需要追加的文件并不在包的当前空间下，则给一个相对路径即可：
```cmake
install(FILES plugin_files/DG3DNavPlugin.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
```
表示追加 plugin_files 文件夹下的 DG3DNavPlugin.xml 文件

---

