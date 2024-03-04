
这个笔记记录了执行bash中的一些坑。

----

#### 文件行结束符号

在bash中读取文件是以 `\n` 作为一行的结束，如果文件中最后一行没有回车，那么会丢失最后一行。

```shell
while IFS= read -r line; do
	echo "$line"
done < "$file_name"
```

```txt
https://github.com/130926C/CppDesignPattern master
https://github.com/130926C/ObsidianBackUp main

```

【注意】：如果使用了vscode打开了文件，那么必须手动添加一个空行，这个问题是vscode导致的，与文件后缀无关。

----

