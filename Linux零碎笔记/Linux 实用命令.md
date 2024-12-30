
该文档介绍了Linux平台下一些实用的命令，旨在快速提供操作。

---
## file

 file 命令在[Linux系统](https://so.csdn.net/so/search?q=Linux%E7%B3%BB%E7%BB%9F&spm=1001.2101.3001.7020)中用于确定文件的类型。它通过检查文件的内容，而不仅仅是文件的扩展名来识别文件类型。
```shell
$ file [-option] file
```

example:
```shell
$ file MyExecutable 

MyExecutable: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=d7789630160484d0460dd1b235330c6ceebbf375, not stripped
```

- **ELF**:  
    表示该文件是一个 ELF（Executable and Linkable Format）文件，这是 Linux 等操作系统中常见的可执行文件、目标文件和共享库的格式。
    
- **64-bit**:  
    表示该文件是 64 位架构下的程序，适用于 64 位系统。
    
- **LSB (Least Significant Byte first)**:  
    表示该文件使用小端字节序（Little Endian），即较低有效字节存储在较小的内存地址中。与之对应的是 MSB（Most Significant Byte first），即大端字节序（Big Endian）。
    
- **shared object**:  
    表示该文件是一个共享对象文件（动态库）。尽管名字是 "shared object"，该描述也可以用于动态链接的可执行文件。
    
- **x86-64**:  
    指出该文件是为 x86-64 架构（64 位 x86 系列 CPU）编译的。
    
- **version 1 (SYSV)**:  
    表示该文件使用 System V ABI（应用二进制接口）的版本 1，这是 UNIX 系统和 Linux 的标准 ABI。
    
- **dynamically linked**:  
    表示该文件是动态链接的。它依赖外部的共享库（如 `libc`），在运行时加载它们。
    
- **interpreter /lib64/ld-linux-x86-64.so.2**:  
    表示在运行时，该程序由动态链接器 `/lib64/ld-linux-x86-64.so.2` 加载并运行。动态链接器负责加载依赖的共享库。
    
- **for GNU/Linux 3.2.0**:  
    表示该文件是为 GNU/Linux 系统版本 3.2.0 或更高版本设计的。较旧的系统可能无法运行该文件。
    
- **BuildID[sha1]=d7789630160484d0460dd1b235330c6ceebbf375**:  
    表示该文件的构建 ID，是由构建工具生成的一个唯一标识符。它通常用于调试或符号解析。
    
- **not stripped**:  
    表示该文件中仍包含调试符号或其他未去除的元数据。调试符号在排查问题或使用调试器时很有用，但会增加文件的大小。如果一个文件被 "stripped"，则这些符号会被移除以减小文件大小。

---
## zip & unzip

压缩与解压缩命令。

```shell
$ zip [option] [generated filename] [want process file/folder]
```

example:
```shell
$ zip -r data.zip data/
$ zip archive.zip file1 file2 file3
```

列出压缩文件中的全部内容：
```shell
$ unzip -l data.zip
```
查看指定层级的文件信息：
```shell
$ unzip -l data.zip | grep "^.*dir1/[^/]*$"
```


---
## 向文件中追加信息

使用下面的命令向一个文件末尾添加信息：
```shell
$ echo "last secents" >> filename
```

---
## 清空文件

使用下面的命令将一个文件清空，但仍然保留文件名：
```shell
$ > filename
```

---
## 找到除指定文件名以外的文件

实用下面的命令搜索除指定文件名以外的所有文件：
```shell
$ find . -type f ! -name "filename"
```

---
## 删除除指定文件名以外的文件

想要删除除指定文件名以外的文件，需要以组合的方式实现：
```shell
$ find . -type f ! -name "filename" -exec rm -f {} +
```

* `-exec rm -f {} +`
    - `rm -f`: 强制删除，无需确认；
    - `{}`: 表示当前匹配的文件名；
    - `+`: 高效批量传递文件名给 `rm`；

---
## 临时关闭代理

```shell
$ export http_proxy=""
$ export https_proxy=""
```

---
## rar 文件解压

```shell
$ unrar x filename.zip
```

---

## Ubuntu 文件管理器显示隐藏文件

在文件管理器中按下 `Ctrl` + `H` 可以显示或隐藏被隐藏文件。

----