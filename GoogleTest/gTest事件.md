
link1: https://blog.csdn.net/sinat_33101665/article/details/128935847
link2: https://blog.csdn.net/A_L_A_N/article/details/106952145

在Google Test框架中，有 **三种** 事件机制：
* 全局事件（针对整个测试程序）；
* 局部事件（针对一个个测试套件）；
* 个体事件（针对一个个测试用例）；

|  | 基类 | 成员函数 | main necessary |
| ---- | ---- | ---- | ---- |
| 全局事件 | `testing::Environment` | `virtual void SetUp()`<br>`virtual void TearDown()`  | ✅ |
| 局部事件 | `testing::Test` | `static void SetUpTestSuite()`<br>`static void TearDownTestSuite()` | ❌ |
| 个体事件 | `testing::Test` | `virtual void SetUp()`<br>`virtual void TearDown()` | ❌ |

----
#### 全局事件 （Global 级）

需要继承 `testing::Environment` 类，并且实现 `SetUp()` 和 `TearDown()` 两个虚函数；全局事件的作用是 **初始化全局配置** & **回收全局资源**：

* `virtual void SetUp()` ：在所有测试开始前执行；
* `virtual void TearDown()` ：在所有测试结束后执行；

定义两个需要执行全局配置的环境：
```cpp
class MyEnvConfigOne: public testing::Environment{
    public:
        virtual void SetUp(){
            printf("MyEnvConfig-One SetUp called\n");
        };
        virtual void TearDown(){
            printf("MyEnvConfig-One TearDown called\n");
        };
};

class MyEnvConfigTwo: public testing::Environment{
    public:
        virtual void SetUp(){
            printf("MyEnvConfig-Two SetUp called\n");
        };
        virtual void TearDown(){
            printf("MyEnvConfig-Two TearDown called\n");
        };
};
```

定义两个测试组：
```cpp
TEST(GlobalTest1, test){
    EXPECT_EQ(1, 1);
};

TEST(GlobalTest2, test){
    EXPECT_EQ(2,2);
};
```

【在主函数中注册全局环境变量】：

```cpp
int main(int argc, char** argv){
    testing::AddGlobalTestEnvironment(new MyEnvConfigOne());
    testing::AddGlobalTestEnvironment(new MyEnvConfigTwo());

    testing::InitGoogleTest(&argc, argv);

    return RUN_ALL_TESTS();
};
```

如果想要使用全局环境变量的话必须添加主函数，并将环境变量增加到测试中：
*  `testing::AddGlobalTestEnvironment`

执行测试：
```shell
$ cmake --build build
$ ./build/demo

[==========] Running 2 tests from 2 test suites.
# Initialize Global Environment Config.
[----------] Global test environment set-up.
MyEnvConfig-One SetUp called
MyEnvConfig-Two SetUp called

# Test 1
[----------] 1 test from GlobalTest1
[ RUN      ] GlobalTest1.test
[       OK ] GlobalTest1.test (0 ms)
[----------] 1 test from GlobalTest1 (0 ms total)
# Test 2
[----------] 1 test from GlobalTest2
[ RUN      ] GlobalTest2.test
[       OK ] GlobalTest2.test (0 ms)
[----------] 1 test from GlobalTest2 (0 ms total)

# Destruct Global Environment Config
[----------] Global test environment tear-down
MyEnvConfig-Two TearDown called
MyEnvConfig-One TearDown called

# Test static
[==========] 2 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 2 tests.
```

---
#### 局部事件 （TestSuite 级）

如果并不需要全局配置环境变量，可以使用 `TEST_F` + 继承`testing::Test` 方式实现在 **指定测试组件** 中的环境配置；

* `static void SetUpTestSutie()`函数是在测试套件第一个测试用例开始前执行；
* `static void TearDownTestSuite()`函数是在测试套件最后一个测试用例结束后执行；

定义一个局部测试配置：
```cpp
class LocalEnvConfig: public testing::Test{
    public:
        static void SetUpTestSuite(){
            printf("Local Env Config SetUpTestCase called\n");
        };
        static void TearDownTestSuite(){
            printf("Local Env Config TearDownTestCase called\n");
        };
};
```

定义三个测试：
```cpp
# 局部配置生效
TEST_F(LocalEnvConfig,test1){
    EXPECT_EQ(1, 1);
};
# 局部配置生效
TEST_F(LocalEnvConfig, test2){
    EXPECT_EQ(3,3);
};

# 局部配置失效
TEST(OutSide, test){
    EXPECT_EQ(10,10);
};
```

执行：
```shell
$ cmake --build build
$ ./build/demo

Running main() from /Users/gaohao/Desktop/exercise/src/google_test/build/_deps/googletest-src/googletest/src/gtest_main.cc
[==========] Running 3 tests from 2 test suites.
[----------] Global test environment set-up.

# Local Env Config Test
[----------] 2 tests from LocalEnvConfig
Local Env Config SetUpTestCase called
[ RUN      ] LocalEnvConfig.test1
[       OK ] LocalEnvConfig.test1 (0 ms)
[ RUN      ] LocalEnvConfig.test2
[       OK ] LocalEnvConfig.test2 (0 ms)
Local Env Config TearDownTestCase called
[----------] 2 tests from LocalEnvConfig (0 ms total)

# OutSide Test
[----------] 1 test from OutSide
[ RUN      ] OutSide.test
[       OK ] OutSide.test (0 ms)
[----------] 1 test from OutSide (0 ms total)

[----------] Global test environment tear-down

# Test Static
[==========] 3 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 3 tests.

```

【Note】：局部变量配置仅在同名测试组中生效，超过作用域后立即失效。

---

#### 个体事件 （TestCase 级）

个体事件其实也是局部事件，与局部事件的差异在于每次进入一个测试实例都会重新执行一次，其实就是 `gTest基本用法` 中的最早实现的几个 `TEST_F` 例子。

定义一个个体事件：
```cpp
class IndivialEnvConfig: public testing::Test{
    public:
        virtual void SetUp(){
            printf("Indivial Env Config called\n");
        };
        virtual void TearDown(){
            printf("Indivial TearDown Config called\n");
        };
};
```

定义测试：
```cpp
TEST_F( IndivialEnvConfig, test1){
    EXPECT_EQ(1,1);
};

TEST_F( IndivialEnvConfig, test2){
    EXPECT_EQ(2,2);
}

TEST(OutSide, test0){
    EXPECT_EQ(10, 10);
};
```

执行测试：
```shell
$ cmake --build build
$ ./build demo

Running main() from /Users/gaohao/Desktop/exercise/src/google_test/build/_deps/googletest-src/googletest/src/gtest_main.cc
[==========] Running 3 tests from 2 test suites.
[----------] Global test environment set-up.
[----------] 2 tests from IndivialEnvConfig

# Indivial Env Test 1
[ RUN      ] IndivialEnvConfig.test1
Indivial Env Config called
Indivial TearDown Config called
[       OK ] IndivialEnvConfig.test1 (0 ms)
[ RUN      ] IndivialEnvConfig.test2
Indivial Env Config called

# Indivial Env Test 2
Indivial TearDown Config called
[       OK ] IndivialEnvConfig.test2 (0 ms)
[----------] 2 tests from IndivialEnvConfig (0 ms total)

# OutSide Test
[----------] 1 test from OutSide
[ RUN      ] OutSide.test0
[       OK ] OutSide.test0 (0 ms)
[----------] 1 test from OutSide (0 ms total)

# Test Static
[----------] Global test environment tear-down
[==========] 3 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 3 tests.
```

