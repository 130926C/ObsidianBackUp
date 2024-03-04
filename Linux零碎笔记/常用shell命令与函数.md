
这个文档用来记录一些常用的shell脚本命令和函数，如果没有额外的链接注释则为ChatGTP输出结果，但都能够保证功能是正常运行的。

----
#### Note

下面的函数正确调用方式：
```shell
$  result=$(check_network "baidu.com" 3)
$  echo $?          # 这个值是函数的返回值0/1
$  echo ${result}   # 这个值是上一条命令的所有输出结果
```

---

#### 脚本启动后弹出警告信息

```shell
#!/bin/bash

# 打印大段信息
cat << EOF
=======================================================================
[Warning]: This is robot auto deploy scripts [Online] version.
Author: gaohao zhou
Email : gaohaozhou@deepglint.com
Modify: 2024-02-26 17:03

[Note]: 
	This script will do those operation below:
		1. Install some necessary libraries from network, please ensuer your network state is aviabel and stable.
		2. Install some drivers and third party software from folder [drivers].
		3. Pull source code, comple and install from gitlab.

	After all, you might need to rebot computer manually.

	If you have any trouble when using this scripts, contact with me.
=======================================================================
EOF

read -p "[Warning]: Authorization ? (y/n): " choice

if [ "$choice" != "y" ] && [ "$choice" != "Y" ]; then
    echo "[Warning]: Operation has been cancled" 
    exit 1
fi

echo "[Success]: Operation has been authorized."
# commands
exit 0
```

---
#### 使用密码

```shell
#!/bin/bash

password="your_password"  # 替换为你的sudo密码

# 使用echo将密码传递给sudo
echo "$password" | sudo -S command1
echo "$password" | sudo -S command2
echo "$password" | sudo -S command3

# 如果你的sudo命令在同一行上，可以这样写：
echo "$password" | sudo -S command1 && echo "$password" | sudo -S command2 && echo "$password" | sudo -S command3
```

----
#### 测试某个地址是否可达

```shell
check_network() {
    local address="$1"          # 第一个参数为远程服务器的IP地址或域名
    local ping_timeout="$2"     # 第二个参数为 ping 命令的超时时间（单位：秒）

    # 检查是否提供了足够的参数
    if [ -z "$address" ] || [ -z "$ping_timeout" ]; then
        echo "[Error]: Params is not enough：check_network <remote IPv4 address> <timeout seconds>"
        return 1
    fi

    # 使用正则表达式检查传入的地址是否符合IPv4格式或域名格式
    if ! [[ "$address" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]] && ! [[ "$address" =~ ^[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
        echo "[Error]: address [$address] is ilegal"
        return 1
    fi

    # 执行 ping 命令并检查返回值
    ping -c 1 -W $ping_timeout $address > /dev/null 2>&1

    if [ $? -eq 0 ]; then
        echo "[Success]: remote address [$address] is aviable."
        return 0
    else
        echo "[Error]: remote address [$address] is not aviable."
        return 1
    fi
}
```

----

#### 安装指定库包

配置文件 `config.conf`
```conf
vim
net-tools
openssh-server
```

命令文件 
```shell
install_libraries_from_config() {
    local config_file="$1"
    # 检查是否提供了配置文件
    if [ -z "$config_file" ]; then
        echo "[Error]: No configuration file has been provided: install_libraries_from_config <config file>"
        return 1
    fi
    # 检查配置文件是否存在
    if [ ! -f "$config_file" ]; then
        echo "[Error]: Configuration [$config_file] is not exist"
        return 1
    fi
    
    # 读取配置文件
    while IFS=, read -r library version; do
        echo "Installing $library ..."
        # 判断是否提供了版本号，如果没有则默认安装最新版本
        if [ -n "$version" ]; then
            echo "  [$library] version=$version"
            # 添加安装逻辑，根据需要调用相应的包管理工具，比如 apt、yum、brew，或者直接下载并编译源代码
            # 示例：使用 apt 安装指定版本
            echo "$password" | sudo -S apt-get install -y $library=$version > /dev/null 2>&1
        else
            echo "  [$library] is use default version"
            # 添加安装逻辑，根据需要调用相应的包管理工具，比如 apt、yum、brew，或者直接下载并编译源代码
            # 示例：使用 apt 安装最新版本
            echo "$password" | sudo -S apt-get install -y $library > /dev/null 2>&1
        fi

        # 检查上一条命令的退出状态
        if [ $? -ne 0 ]; then
            echo "[Error]: Installing [$library] failed"
            return 1
        fi
    done < "$config_file"

    echo "[Successed]: All libraries install successed!"
    return 0
}
```

---

#### 检查文件是否存在

```shell
check_file_exists() {
    local file_path="$1"
    # 检查是否提供了文件路径参数
    if [ -z "$file_path" ]; then
        echo "[Error]: Input file path."
        return 1
    fi
    # 检查文件是否存在
    if [ -e "$file_path" ]; then
        echo "[Success]: file '[$file_path]' exist."
        return 0  # 返回 0 表示文件存在
    else
        echo "[Error]: file '[$file_path]' not exist"
        return 1  # 返回 1 表示文件不存在
    fi
}
```

----

#### 安装deb包（单个）

```shell
install_single_deb_package() {
    local deb_package="$1"

    # 检查是否提供了deb包路径参数
    if [ -z "$deb_package" ]; then
        echo "[Error]: Input deb pachage file."
        return 1
    fi

    # 检查deb包是否存在
    if [ ! -f "$deb_package" ]; then
        echo "[Error]: deb file [$deb_package] is not exist."
        return 1
    fi

	echo "Installing package [$deb_package]..."
    # 使用dpkg命令安装deb包
    echo "$password" | sudo -S dpkg -i "$deb_package"

    # 检查安装是否成功
    if [ $? -eq 0 ]; then
        echo "[Success]: Install package [$deb_package] done."
        return 0
    else
        echo "[Error]: Install package [$deb_package] failed."
        return 1
    fi
}
```

---

#### 安装deb包（多个）

```shell
install_deb_packages_in_folder() {
    local folder_path="$1"

    # 检查是否提供了文件夹路径参数
    if [ -z "$folder_path" ]; then
        echo "[Error]: Input deb packages folder."
        return 1
    fi

    # 检查文件夹是否存在
    if [ ! -d "$folder_path" ]; then
        echo "[Error]: deb packages folder is not exsit"
        return 1
    fi

    # 遍历文件夹中的deb包
    for deb_package in "$folder_path"/*.deb; do
        # 检查deb包是否已经安装
        if dpkg -l | grep -q $(dpkg-deb -f "$deb_package" Package); then
            echo "[Success]:  $(dpkg-deb -f "$deb_package" Package) is already installed."
        else
            # 使用dpkg命令安装deb包
            echo "[Warning]: Installing $(dpkg-deb -f "$deb_package" Package) ..."
            echo "$password" | sudo -S dpkg -i "$deb_package"

            # 检查安装是否成功
            if [ $? -ne 0 ]; then
                echo "[Error]: Install [$deb_package] failed."
                return 1
            fi
        fi
    done

    echo "[Success]: all deb package install done."
    return 0
}
```

---

#### 更换国内源

```shell
change_sources_list() {
    # 设置源文件路径
    local sources_list_path="/etc/apt/sources.list"
	local replace_list_path="./sources.list"
	
    # 检查文件是否存在
    if [ -f "$sources_list_path" ]; then
        # 如果文件存在，备份为sources_back.list
        echo "$password" | sudo -S cp "$sources_list_path" "${sources_list_path}_back"
        echo "[Warning]: [sources.list] is exist, backing up to [sources_back.list]..."
    else
        # 如果文件不存在，创建文件
        echo "$password" | sudo -S touch "$sources_list_path"
        echo "[Warning]: creating [sources.list]..."
    fi

	# 将当前的源文件直接替换过去
	echo "$password" | sudo -S rm $sources_list_path
	echo "$password" | sudo -S mv $replace_list_path "/etc/apt/"

    echo "[Successed]: sources.list content is updated."
    echo "Ready to update apt sources list..."

	echo "$password" | sudo -S apt-get update

    return 0
}
```

---

#### 检查当前系统是否是Ubuntu-18.04桌面版 & amd64架构 （有bug）

```shell
check_ubuntu_18_04_desktop_amd64() {
    # 检查是否是 Ubuntu 系统
    if [ -r /etc/os-release ]; then
        source /etc/os-release
        if [ "$ID" != "ubuntu" ]; then
            echo "[Error]: Current system is NOT [Ubuntu], auto deploy is NOT SUPPORT"
            return 1
        fi

        # 检查 Ubuntu 版本
        if [ "$VERSION_ID" != "18.04" ]; then
            echo "[Error]: Current system is NOT [Ubuntu] Version=18.04, NOT SUPPORT"
            return 1
        fi

        # 检查架构是否是 x86_64 架构
        architechture=$(uname -m)
        if [ "$architechture" != "x86_64" ]; then
	        echo "[ERROR]: Current system is NOT [x86_64] arch, NOT SUPPORT"
			return 1
			
        # 检查是否是桌面版
        if [ "$XDG_CURRENT_DESKTOP" != "ubuntu:GNOME" ]; then
            echo "[ERROR]: Current system is NOT [Ubuntu]-desktop, NOT SUPPORT"
            return 1
        fi

        echo "[Success]: Ubuntu 18.04-Desktop，amd64 dected."
        return 0
    else
        echo "[Error]: can't read file [/etc/os-release]"
        return 1
    fi
}
```


---
#### 检查包是否安装过

```shell
check_package_installation() {
    package_name="$1"
    # Check if dpkg-query command is available
    if command -v dpkg-query >/dev/null 2>&1; then
        # Check if the package is installed
        if dpkg-query -W -f='${Status}' "$package_name" 2>/dev/null | grep -q "install ok installed"; then
            echo "$package_name is installed."
            return 0  # Package is installed, return 0
        else
            echo "[Warning]: $package_name is not installed."
            return 1
        fi
    else
        echo "[Error]: dpkg-query command not found. Unable to check package installation."
        return 1
    fi
}
```

---

#### 检查ROS环境是否安装，如果没有则安装

```shell
check_and_install_ros_enviroment(){
    # Check if rosversion command is available
    if command -v rosversion >/dev/null 2>&1; then
        # Check ROS version
        ros_version=$(rosversion -d 2>/dev/null)
        if [ $? -eq 0 ]; then
            echo "ROS version: $ros_version"
            return 0  # ROS is installed, return 0
		fi
	fi
	echo "No ROS env detected, ready to installing"

wget http://fishros.com/install -O fishros && . fishros 4>&1 << EOF
1
2
1
1
EOF
	
wget http://fishros.com/install -O fishros && . fishros << EOF
3
EOF
	
	rosdepc update
    echo "ROS env install done"
}
```

----

#### 根据脚本顺序逐个执行脚本

这段代码要求文件结构如下（结构不可变，顺序文件必须和脚本在同级目录下）：
```shell
.
├── compile_order.txt    # 脚本执行顺序文件
├── eigen_compile.sh     # 需要执行的脚本
├── moveit_compile.sh
├── open3d_compile.sh
└── opencl_compile.sh
```

其中执行顺序文件内容如下：
```txt
eigen_compile.sh
moveit_compile.sh
open3d_compile.sh
opencl_compile.sh
```

功能函数如下：
```shell
execute_scripts_by_order(){
    local execute_order_file=$1
    echo "scripts folder is $execute_order_file"
    # 检查传入的文件是否存在
    if [ ! -f "$execute_order_file" ]; then
        echo "[Error]: Script file not found: $execute_order_file"
        return 1
    fi
    # 从传入的路径中将所在目录分离
    local directory_path=$(dirname "$execute_order_file")
    echo "directory path is $directory_path"
    # 执行同级目录脚本

    while IFS= read -r script; do
        script_path="$directory_path/$script"  # 拼接脚本的完整路径
        echo "[Warning]: Executing script: $script_path"
        if bash "$script_path"; then
            echo "[Success]: Script $script_path executed successfully."
        else
            echo "[Error]: Script $script_path failed. Exiting."
            return 1
        fi
    done < "$execute_order_file"

    echo "All scripts executed successfully."
    return 0
}
```

调用：
```shell
$ execute_scripts_by_order "./sources/compile_order.txt"
```

---

#### 根据时间戳生成日志文件

```shell
generate_file_with_timestamp() {
    local target_directory="$1"

    if [ -z "$target_directory" ]; then
        echo " Error: Target directory not specified. "
        return 1
    fi

    # 生成当前时间戳
    local timestamp=$(date +"%Y%m%d%H%M%S")
    # 构建文件名
    local file_path="${target_directory}/${timestamp}.txt"
    # 创建文件
    touch "$file_path"
    echo "${file_path}"
    return 0
}
```

使用：
```shell
LOG_FILE=$(generate_file_with_timestamp "./logs")
```

---

#### 检查某个进程是否正在运行

```shell
check_process_working(){
	local SERVICE=$1
	if ps ax | grep -v grep | grep $SERVICE > /dev/null
	then
	    echo "$SERVICE service running, everything is fine"
	else
	    echo "$SERVICE is not running"
	    echo "$SERVICE is not running!" | mail -s "$SERVICE down" root
	fi
}
```

---

