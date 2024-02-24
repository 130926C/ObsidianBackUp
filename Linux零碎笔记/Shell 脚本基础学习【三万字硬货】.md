
本笔记来源知乎博主 [勤奋的小牛](https://www.zhihu.com/people/qing-huan-89-95-32) 文章 [Shell 脚本基础学习【三万字硬货】](https://zhuanlan.zhihu.com/p/651764421) 

---

#### 脚本解释器

shell脚本都是以 `#!` 开头的，告知系统该文件的执行需要一个解释器：
```bash
$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/bin/rbash
/bin/dash
```

使用下面的命令可以获得当前系统的解释器：
```bash
$ echo $SHELL
/bin/bash
```

---
#### 调用脚本

执行脚本的三种方式：

```shell
# 方式一
sh hello_world.sh
# 方式二
bash hello_world.sh
bash +x hello_world.sh
# 方式三
./hello_world.sh
```

对脚本进行权限授予：
```bash
chmod +x hello_world.sh     # 可执行
chmod +rx hello_world.sh    # 任何人可执行和可读
chmod u+rx hello_world.sh   # 只给脚本所有者可执行和可读
```

----

#### 第一个脚本

```shell
#!/bin/bash
echo "Hello World"
```

```shell
$ chmod u+rx scripts.sh
$ ./scripts.sh
```

在Linux/Unix系统中，普通脚本的第一行是： `#!/bin/sh` 或 `#!/bin/bash`。

```bash
#!/bin/bash
echo "All input params: " $@     # 打印所有传入的参数
echo "Input params count" $#     # 打印传入参数的个数
echo "$0=" $0     # 第0个参数是当前脚本文件名
echo '$1=' $1     # 第1个参数是传入的第一个参数
echo '$2=' $2     # 如果第二个参数不存在则打印一个空字符串
```

```shell
$ ./scripts.sh
$ 
	All input params: input_string
	Input params count 1
	./temp.sh= ./temp.sh
	$1= input_string
	$2=
```

【注意】`""`中的内容会自动解析里面的变量，`''`中的内容才是纯字符串。

```shell
#!/bin/bash
for i in "$@";
        do echo 'variable: ' $i
done
```

```shell
$ ./temp.sh a b c
	variable:  a
	variable:  b
	variable:  c
```

如果用`""`包起来会被视为一个命令：
```shell
$ ./temp.sh "a b c"
	variable:  a b c
```

---

#### 注释

单行注释
```shell
# comment
echo "hello world"    # comment
```

多行注释
```shell
:<<EOF
those is comment
those is comment too
EOF
```

---

#### 命令执行结果

命令执行结束后会有一个返回值，`0`表示成功，`非0`表示失败，环境变量 `$?` 可以用来读取前一个命令的返回值，`1>&2`是将标准输出的文件描述符`1`重定向到标准错误输出的文件描述符`2`中：

```shell
#!/bin/bash
#!/bin/bash
# cd /path/to/somewhere
cd /root/Downloads
if [ "$?" = 0 ]; then
        echo "enter somewhere successed"
else
        echo "enter somewhere failed" 1>&2
fi
```

【注意】在`if`语句后面的 `[ "$?" = 0 ]` 必须有括号，否则会运行错误。

执行 `cd /path/to/somewhere` 失败：
```shell
./temp.sh: line 2: cd: /path/to/somewhere: No such file or directory
enter somewhere failed
```

执行 `cd /root/Downloads` 成功：
```shell
enter somewhere successed
```

也可以使用逻辑符来简化操作：
```shell
#!/bin/bash
cd /path/to/somewhere && echo "enter somewhere folder successed."
cd /root/Desktop && echo "enter Desktop folder successed"
```

```shell
./temp.sh: line 2: cd: /path/to/somewhere: No such file or directory
enter Desktop folder successed
```

----

#### 常见指令

##### `echo` 用来显示消息或输出其他命令的结果：

```shell
echo [-neE] [arguments]
```

* `-n` 取消尾随换行符；
* `-e` 将解释以下反斜杠转移字符：
	* `\` 显示反斜杠转义符；
	* `\a` 警报（BEL）；
	* `\b` 显示退格字符；
	* `\c` 禁止任何进一步的输出；
	* `\e` 显示转义字符；
	* `\f` 显示窗体提要字符；
	* `\n` 显示新行；
	* `\r` 显示回车；
	* `\t` 显示水平标签；
	* `\v` 显示垂直标签；
* `-E` 禁用转义字符的结束（默认存在）

如果想要打印双引号需要使用单引号将其包括在内：
```shell
#!/bin/bash
echo 'hello "world"'
```

##### 模式匹配字符

`echo` 命令可以与模式匹配一起使用，如下面的命令将返回当前目录下所有的 `*.cpp` 文件名：
```shell
$ tree 
	.
	├── temp.sh
	├── tools.cpp
	├── waper.py
	└── warap.cpp
```

```shell
$  echo The cpp file is here: *.cpp
	The cpp file is here: tools.cpp warap.cpp
```

【注意】这里并没有写一个shell脚本，而是直接在终端中写命令。

##### 显示变量

也可以用 `echo` 显式变量：
```shell
$  echo $USER
$  root
```

##### 显示命令的输出

```shell
$  echo "Current date is: $(date +%D)"
	Current date is 02/24/24

$  echo "Today is $(date +%Y-%m-%d-%H:%M:%S)"
	Today is 2024-02-24-22:26:48
```

【注意】在上面的命令中 `+%D` 中间不能有括号。

##### `env` - 环境变量

`env`命令总是指向`/usr/bin/env`文件，命令参数如下：

* `i`：`-ignore-enviroment` 不使用环境变量启动；
* `u`：`-unset=NAME` 从环境变量中删除一个变量；
* `-help`：显示帮助文档；
* `-version`：输出版本信息；

```shell
$  env -i /bin/sh 
```

命令输入后会打开一个新的解释器，这个解释器配置是上面指令。

##### export指令 - 设置或显示环境变量

在shell执行程序时，shell会提供一组环境变量，`export`可以新增、修改、删除环境变量，**退出了当前的解释器后就会失效**。

```shell
$  export -p
	declare -x CONDA_DEFAULT_ENV="base"
	declare -x CONDA_EXE="/root/miniconda3/bin/conda"
	...
	declare -x _CE_CONDA=""
	declare -x _CE_M=""
```

添加一个环境变量：
```shell
$  export AA=2
$  export -p | grep AA
```

改变变量值：
```shell
$  export AA=5
$  export -p | grep AA
```

删除环境变量：
```shell
$  unset AA
$  export -p | grep AA
```

同样也可以在文件中添加环境变量：
```shell
export AA=5
echo $AA
```

##### `shift` - 改变脚本参数

`shift`可以改变脚本参数，每次执行都会移除当前脚本上的第一个参数`$1`，使得后面的参数向前移动一位：

```shell
#!/bin/bash

echo "Total Input $# params"
while [ "$1" != "" ]; do
        echo "remain $# params"
        echo "param: $1"
        shift
done
```

```shell
$  ./script.sh string-1 string-2 string-3 string-4
	Total Input 4 params
	remain 4 params
	param: string-1
	remain 3 params
	param: string-2
	remain 2 params
	param: string-3
	remain 1 params
	param: string-4
```

也可以给 `shift`加入一个参数用来删除多个变量，如果输入的量小于这个参数则会一直在循环，所以最好加入一个 `break` 当命令执行失败时就退出：
```shell
shift 3 || break
```

##### `getopts` 解析复杂的脚本命令行参数

这个命令只能在文件中使用，通常和 `while` 一起使用。

```shell
getopts optstring name
```

* `optstring` 是字符串，给出脚本所有的连词线参数；
* `name` 是变量名，用来保存当前获取的配置参数项目；

```shell
#!/bin/bash

while getopts 'lha:' OPTION ; do
        case "$OPTION" in
        l) 
                echo "Linux config"
                ;;
        h)
                echo "h stands for h"
                ;;
        a)
                avalue="$OPTARG"
                echo "The value provided is $OPTARG"
                ;;
        ?)
                echo "script usage: $(baename $0) [-l] [-h] [-a somvevale]" >&2
                exit 1
                ;;
        esac
done
shift "$((($OPTIND -1)))"
```

执行：
```shell
$  ./script -lha:foo
	Linux config
	h stands for h
	The value provided is :foo
```

##### 配置项参数终止符 --/-

`-` 和`--` 开头的参数会被bash当作配置项的解释，如果想要让其不作为解释则需要如下设置：
```shell
$  cat -- -f
$  cat -- --file
$  ls -- $myPath    # 强制变量$myPath只能当作实体参数
```

上面的命令中会将 `-f` 解释称一个命令而不是注释。

##### `declare` 声明一些特殊类型的变量

该命令可以给变量一些特殊设置，比如让变量是只读的或者是整型变量。
```shell
declare OPTION VARIABLE=value
```

其中 `OPTION` 参数如下：
* `-a`：声明数组变量；
* `-f`：输出所有函数定义；
* `-F`：输出所有函数名；
* `-i`：声明整数变量；
* `-l`：声明变量为小写字母；
* `-p`：查看变量信息；
* `-r`：声明只读变量；
* `-u`：声明变量为大写字母；
* `-x`：该变量输出为环境变量；

```shell
$ declare -i val1=12 val2=5
$ declare -i result
$ result=val1*val2
$ echo $result
	60
```

【注意】上面的赋值语句不能有空格。

```shell
$ declare -u foo
$ foo=upper
$ echo $foo
	UPPER
```

```shell
$ declare -l bar
$ bar=LOWER
$ echo $ bar
	lower
```

```shell
#!/bin/bash

function hello(){
        echo "hello"
}

function alice(){
        echo "alice: $@"
        echo "$0: $1 $2 $3"
        echo "$# arguments"
}

echo "========================"
declare -f
echo "========================"
declare -F
```

```shell
$ ./script.sh
	========================
	alice () 
	{ 
	    echo "alice: $@";
	    echo "$0: $1 $2 $3";
	    echo "$# arguments"
	}
	hello () 
	{ 
	    echo "hello"
	}
	========================
	declare -f alice
	declare -f hello
```

##### `readonly` 声明只读变量

`readonly` 等价于 `delcare -r` 用来声明只读变量，不能改变也不能unset：
```shell
$ readonly foo=1
$ foo=2
$ echo $?
```

##### `let` 可以直接执行算数表达式

```shell
$  let foo=1+2
$  echo $foo
```

```shell
#!/bin/bash

a=5

let a+=2
echo "plus: $a"
let a-=2
echo "minus: $a"
let a*=2
echo "multi: $a"
let a/=2
echo "div: $a"
let a%=2
echo "modulo: $a"
```

```shell
plus: 7
minus: 5
multi: 10
div: 5
modulo: 1
```

##### `expr` 指出算数运算

```shell
$ expr 3 + 2
$ foo=3
$ expr $foo + 2
```

【注意】进行算数使用时必须给中间加空格

获取字符串长度
```shell
$ echo 'expr length $str'
```

求字符串索引
```shell
$ expr index "$string" '$substring'
```

##### `source` 执行一个脚本

```shell
$  source ./bashrc
```

同时也可以在脚本内加载外部库：
```shell
$  source ./lib.sh
```

---

#### 操作字符串

bash中 **只有一个数据类型** 就是字符串，字符串有两种输出方式 `''` 单引号和 `""` 双引号：

* 单引号
	* 包括变量都原样输出，并不对变量进行解析，如`$PAHT`输出的就是 `$PATH`；
	* 单引号内不能再使用单引号；
	* 单引号内所有特殊字符都会被解析成普通字符；
* 双引号
	* 双引号内使用的变量会被解析成具体值；
	* 双引号内再使用引号需要用转义字符；
* 不使用引号
	* 性质和双引号一直，但字符串中间不能有空格；
```shell
$  echo "$SHELL"
$  /bin/bash

$  echo "`date`"
$  Sun Feb 25 00:13:42 CST 2024
```

双引号还可以保留原始命令格式：
```shell
$  echo $(cal)
	February 2024 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29

$  echo "$(cal)"
	February 2024      
	Su Mo Tu We Th Fr Sa  
	             1  2  3  
	 4  5  6  7  8  9 10  
	11 12 13 14 15 16 17  
	18 19 20 21 22 23 24  
	25 26 27 28 29   
```

转义字符：
```shell
$  echo a\tb
	atb

$  echo -e "a\tb"
	a       b
```

字符串长度：
```shell
# 格式
${#string}             # 方式一
expr length $string    # 方式二
expr "$string" : '.*'  # 方式三

# 例子
str="Hello World"
echo "${#str}"
echo "$(expr length "$str")"
echo "$(expr "$str" : '.*')"

echo `expr "$str" : '.*'`
```

字符串匹配
```shell
# 格式
expr match "$string" '$substring'
expr "$string" : '$substring'

# 例子
str="asdfg"
echo `expr match "$str" 'abs[A-Z]*.2`
```

【注意】：原博客内容太多，后续继续补充

----

#### 数组

bash只支持 **一维数组** ，数组元素可以使用 `var[number]` 来初始化。
```shell
# 格式
array_name=(element1 element2 element3 ...)

# 例子
arr_odd=(1 3 5 7 9)
```

可以按默认顺讯排序，也可以在前面指定位置索引
```shell
$ array=(a b c)
$ array=([2]=c [0]=a [1]=b)

$ days=(Sun Mon Tue Wed Thu Fri Sat)
$ days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)
```

按照下标索引方式调用
```shell
$  echo ${array[2]}

for i in "${array[*)]}"; do
	echo $i
done
```

打印所有元素
```shell
$  echo ${array[*]}
```

删除元素
```shell
$  unset array[1]   # 删除下标为1的元素
$  unset array      # 删除整个数组
```

拷贝数组
```shell 
$ hobbies=( "${array[*]}" )          # 拷贝全部内容
$ hobbies=( "${array[@]}" diving)    # 拷贝完后再追加一个 diving
```

数组长度
```shell

```