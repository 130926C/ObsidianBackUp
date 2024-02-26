# vim 快捷操作指南

---

#### vim常用模式

- 普通：主要用来浏览文件、复制、粘贴、删除操作；
- 输入：在普通模式下键入 `i`
- 命令：在普通模式下键入 `:`

---

## 普通模式

#### 移动光标

基础

- 上下左右方向键
- `hjkl`：左下上又
- `w`: 到下一个单词开头（word）
- `b`: 到前一个单词开头（begin）
- `^`: 到当前行开头
- `$`: 到当前行尾

高级

- `4j`: 向上移动4行
- `4k`: 向下移动4行
- `gg`: 到文件头
- `G`: 到文件尾
- `ctrl+u`: 向上翻页（up）
- `ctrl+d`: 向下翻页（down）
- `10gg`: 移动到第10行，执行这个命令时不用在命令模式下，直接在普通模式下输入即可，并且不需要键入回车

---

#### 查询操作

- `fr`: 移动到当前光标后面最近的字母r（find r）
- `:/example`: 在当前光标位置向后查找指定字符串`example`并高亮显示
- `:?example`: 在当前光标位置向前查找指定字符串`example`并高亮显示

在搜索模式下键入`n`切换到下一个匹配的位置；键入`N`切换到上一个匹配的位置；

---

#### 替换操作

- `:%s/oldword/newword/g`: **全局**将`oldword`替换成`newword`;
- `:s/oldword/newword/`: **第一个匹配*将`oldword`替换成`newword`;
- `:s/oldword/newword/g`: **当前行**将`oldword`替换成`newword`;

---

#### 全文本格式化对齐

`gg=G`: 在普通模式下直接输入即可，不用理会前面的gg操作导致移动到文件头，继续执行就行

---

#### 撤销 undo

- `u`: 撤销上一次执行的粘贴、删除

#### 重做 redo

- `ctrl+r`: 重做上一次撤销

#### 复制粘贴操作 yank，paste

基本

- `yaw`: 复制一整个单词（yank all word）
- `p`: 粘贴（paste）

高级

- `y4j`: 复制包括当前行在内的下4行内容
- `y4k`: 复制包括当前行在内的上4行内容
- `yfr`: 复制当前到最近的字母r的内容

---

#### 剪切操作

- `d`: 剪切当前行
- `y`: 粘贴到当前行

---

#### 删除操作 delete

- `dd`: 删除当前行
- `d2j`: 删除当前行开始下2行的内容
- `d2k`: 删除当前行开始上2行的内容
- `dfr`: 删除当前行开始到单词r的内容
- `:3,10d`: 删除从第3行到第10行的全部内容
- `:3,$d`: 删除从第3行开始到文件结束的内容

---

## 输入模式

在普通模式下键入 ‘i/a’ 进入输入模式；

基础

- `i`: 在当前光标的前一个字母上输入（input）
- `a`: 在当前光标的后一个字母上输入（append）
- `I`: 在当前行的开头输入
- `A`: 在当前行的末尾输入
- `o`: 在当前行的下一行重新插入一个空白行后输入  

高级
- `caw`: 删除当前词并直接进入输入模式（change all word）
- `daw`: 删除当前词但不进入输入模式（delete all word）
- `cc`: 删除当前行并直接进入输入模式
- `c4j`: 删除当前行开始的下4行并直接进入输入模式

---

## 保存与退出

需要先从普通模式切换到命令模式，输入`:`（冒号）

- `q`: 退出文件（quite）
- `w`: 保存文件（write）

---

## 可视模式

在普通模式下键入`v`进入可视模式

- `hjkl`: 选择光标覆盖范围的内容

可视模式仍然支持复制（yank）、粘贴（paste）、删除（delete）操作、删除并进入直接进入输入模式（change）

#### 通过可视模式实现全选

- `ggVG`: 首先到文件首行，然后进入可视模式，在可视模式下前往文件末尾，这样就可以全选文件。

#### 通过可视模式实现多行注释

1. `ctrl+v`: 进入可视模式的块模式**viual block**;
2. 使用方向键选择需要注释的行；
3. `shift+i`: 进入到被选中的第一行；
4. 输入注释标识符号如`// #`，这里可能只显示在第一行有注释，不用管它继续操作；
5. **连续键入两次** `ESC` 实现多行注释；

#### 通过可视模式实现多行删除注释

1. `ctrl+v`: 进入可视模式的块模式**visual block**;
2. 使用方向键选择需要取消注释的行；
3. 输入`d`直接删除内容；

这种方法本质是通过可视模式的块操作功能实现批量修改，所以不一定必须在行首使用，也可以在任意位置选中区域

#### 通过可视模式选中当前词

`viw`: 选中当前光标所在的词

---

#### 分屏操作

- `:sp`: 上下分屏
- `:vsp`: 左右分屏
- `ctrl+w+h`: 切换到左侧屏幕
- `ctrl+w+j`: 切换到下侧屏幕
- `ctrl+w+k`: 切换到上侧屏幕
- `ctrl+w+l`: 切换到右侧屏幕

---

#### 多文件操作

- `:e Desktop/test.cpp`: 打开`Desktop/test.pp`文件

---

#### 临时配置vim

- `:set number`: 显示行号
- `:set nonumber`: 隐藏行号
- `:set tabstop=4`: 设置制表符宽度为4个字符
- `:set hlsearch`: 设置搜索结果为高亮

---

## 配置vim

如果使用的是原版vim，修改配置文件是通过 `.vimrc` 的文件实现：

1. 可以手动创建一个属于自己定义的配置文件夹 `self_config`；

```
$ mkdir self_config && cd self_config
```

2. 在这个配置文件夹中再创建一个属于vim的配置文件夹：

```
$ mkdir vim && cd vim
```

3. 创建一个`init.vim`的文件

```
$ touch init.vim
```

4. 在这个文件中就可以对vim进行配置

在网站 [vim awesome](https://vimawesome.com/) 中可以找到很多相关的vim配置插件

【Note】：在安装方式时最好使用 **vim plug** 的安装方式

[参考链接](https://zhuanlan.zhihu.com/p/657039661)

```
$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

如果想要查看vim自带的主题，可以在下面找到：
```shell
$ /usr/share/vim/vim90/colors
```

下面这个人的vim配置链接可以参考下：

https://www.zhihu.com/question/383348951/answer/3308689919?utm_id=0

----

#### 升级vim版本【Ubuntu】

参考链接：https://blog.csdn.net/wxd1233/article/details/127823472

Step1. 从github仓库拉取release版本
```shell
$  wget https://github.com/vim/vim/archive/refs/tags/v9.0.0861.tar.gz
```

Step2. 解压
```shell
$  tar -xzvf v9.0.0861.tar.gz
```

Step3. 安装潜在的依赖库
```shell
$  sudo apt install libncurses5-dev
```

Step4. 编译并安装
```shell
$ cd vim-9.0.0861/
$ ./configure --prefix=$HOME/.local --enable-python3interp=yes && make && make install
```

Step5. 链接
```shell
$  alias vim='~/.local/bin/vim'
$  echo "alias vim='~/.local/bin/vim'" >> ~/.bashrc
```

查看版本：
```shell
$  vim --version
```