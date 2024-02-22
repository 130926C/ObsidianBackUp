
link1: https://blog.csdn.net/A_L_A_N/article/details/106952145
link2: https://blog.csdn.net/jiangliuhuan123/article/details/130059154

在gTest中是没有全局范围的异常保护，即如果在普通测试中抛出了段错误也会导致整个程序崩溃：
```cpp
TEST(DemoTest test0){
	int *p = nullptr;
	*p = 1;
};
```

执行输出 **段错误**
```shell
Running main() from /Users/gaohao/Desktop/exercise/src/google_test/build/_deps/googletest-src/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from DemoTest
[ RUN      ] DemoTest.test0
Segmentation fault: 11
```

但指针异常确实是测试项目中的一个需求，因此引入了死亡测试。

---

#### 简单例子

* `EXPECT_DEATH` ：不会中断程序
* `ASSERT_DEATH`：中断程序

* 方式一：使用函数地址传入 
```cpp
void func(){
	int *p = nullptr;
	*p = 10;
}

TEST(DemoTest, test0){
	EXPECT_DEATH(func(), "");
};

```

* 方式二：使用lambda表达式
```cpp
TEST(DemoTest, test1){
	EXPECT_DEATH(
		{
			int* p = nullptr;
			*p = 10;
		},
		""
	);
}
```

【Note】这里传入的函数一定是 `func()`，不能是 `func`

执行结果：
```shell
Running main() from /Users/gaohao/Desktop/exercise/src/google_test/build/_deps/googletest-src/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from DemoTest
[ RUN      ] DemoTest.test0
[       OK ] DemoTest.test0 (1 ms)
[----------] 1 test from DemoTest (1 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (1 ms total)
[  PASSED  ] 1 test.
```

---

