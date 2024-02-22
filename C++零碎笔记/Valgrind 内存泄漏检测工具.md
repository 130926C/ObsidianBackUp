
link1: https://blog.csdn.net/lkj345/article/details/50913895

---

#### 简单使用示例

```cpp
int main(){
	int *x = (int*)malloc(100 * sizeof(int));
	x[0]=0;
	return 0;
}
```

执行命令
```shell
$ g++ main.cpp -o a
$ valgrind --tool=memcheck --leak-check=full --show-reachable=yes ./a

==11636== Memcheck, a memory error detector
==11636== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==11636== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==11636== Command: ./test
==11636== 
==11636== 
==11636== HEAP SUMMARY:
==11636==     in use at exit: 400 bytes in 1 blocks
==11636==   total heap usage: 2 allocs, 1 frees, 73,104 bytes allocated
==11636== 
==11636== 400 bytes in 1 blocks are definitely lost in loss record 1 of 1
==11636==    at 0x4C31B0F: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==11636==    by 0x10879B: main (in /root/Desktop/vim_test/test)
==11636== 
		  # 内存泄漏报告 
==11636== LEAK SUMMARY:
==11636==    definitely lost: 400 bytes in 1 blocks
==11636==    indirectly lost: 0 bytes in 0 blocks
==11636==      possibly lost: 0 bytes in 0 blocks
==11636==    still reachable: 0 bytes in 0 blocks
==11636==         suppressed: 0 bytes in 0 blocks
==11636== 
==11636== For counts of detected and suppressed errors, rerun with: -v
==11636== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

修改代码后
```cpp
int main(){
	int *x = (int*)malloc(100 * sizeof(int));
	x[0]=0;
	// 回收资源
	free(x);
	x = nullptr;
	return 0;
}
```

```shell
$ g++ main.cpp -o a
$ valgrind --tool=memcheck --leak-check=full --show-reachable=yes ./a

==11876== Memcheck, a memory error detector
==11876== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==11876== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==11876== Command: ./test
==11876== 
==11876== 
==11876== HEAP SUMMARY:
==11876==     in use at exit: 0 bytes in 0 blocks
==11876==   total heap usage: 2 allocs, 2 frees, 73,104 bytes allocated
==11876== 
		  # 内存没有泄漏
==11876== All heap blocks were freed -- no leaks are possible
==11876== 
==11876== For counts of detected and suppressed errors, rerun with: -v
==11876== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```