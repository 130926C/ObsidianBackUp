
link1: https://zhuanlan.zhihu.com/p/544491071
link2: https://www.zhihu.com/search?type=content&q=gtest
link3: https://blog.csdn.net/A_L_A_N/article/details/106952145
link4: https://blog.csdn.net/qq_23041903/article/details/117985087
link5: https://blog.csdn.net/wteruiycbqqvwt/article/details/125625975
link6: https://blog.csdn.net/rheostat/article/details/5390912

* GoogleTest 官方入门手册：https://google.github.io/googletest/quickstart-cmake.html

这个手册的阅读顺序推荐如下：
1. Quickstart: Building with CMake；
2. GoogleTest Primer；

---

#### 关于主函数

通常不需要编写自己的 `main` 函数，编译器会自动将它链接到 `gtest_main`。如果有特殊需求，需要编写一个 `main` 函数，则需要在返回时调用 `RUN_ALL_TESTS()` 来运行所有测试：

```cpp
int main(int argc,  char** argv){
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
};
```

【注意】：如果写了主函数，则必须写 `return RUN_ALL_TESTS()` ，否则所有测试都不会被运行。

----
#### Google Test基本宏定义

link： https://blog.csdn.net/rheostat/article/details/5390912

通常最常用的两个宏定义为：
* `ASSERT_*`:在失败时会产生**致命错误**，并中止当前函数，同一用例后面的语句将不再执行；
* `EXPECT_*`:在失败时会产生 **非致命故障**，不会中止当前功能；
##### 基本断言
```cpp
// _condition_为真
ASSERT_TRUE(_condition_);  
EXPECT_TRUE(_condition_);  

// _condition_为假
ASSERT_FALSE(_condition_);  
EXPECT_FALSE(_condition_);  
```

##### 二进制比较
```cpp
// [equal]       _expected_==_actual_  
ASSERT_EQ(_expected_,_actual_);  
EXPECT_EQ(_expected_,_actual_);  

// [not equal]   _val1_ != _val2_  
ASSERT_NE(_val1_,_val2_);  
EXPECT_NE(_val1_,_val2_);  

// [less than]   _val1_ < _val2_ 
ASSERT_LT(_val1_,_val2_);  
EXPECT_LT(_val1_,_val2_);  

// [less equal]  _val1_ <= _val2_
ASSERT_LE(_val1_, _val2_);
EXPECT_LE(_val1_, _val2_);

// [greate than] _val1_ > _val2_
ASSERT_GT(_val1_, _val2_);
EXPECT_GT(_val1_, _val2_);

// [greate equal] _val1_ >= _val2_
ASSERT_GT(_val1_, _val2_);
EXPECT_GT(_val2_, _val2_);
```

##### 字符串检查
```cpp
// [euqal] 两个 C 字符串有相同的内容 
ASSERT_STREQ(_expected_str_,_actual_str_);  
EXPECT_STREQ(_expected_str_,_actual_str_);  // 期待字符串相等，不想等则失败

// [not equal ]两个 C 字符串有不同的内容
ASSERT_STRNE(_str1_,_str2_);  
EXPECT_STRNE(_str1_,_str2_);  

// [equal] 两个 C 字符串有相同的内容，忽略大小写  
ASSERT_STRCASEEQ(_expected_str_,_actual_str_);  
EXPECT_STRCASEEQ(_expected_str_,_actual_str_);  

// [not equal] 两个 C 字符串有不同的内容，忽略大小写
ASSERT_STRCASENE(_str1_,_str2_);  
EXPECT_STRCASENE(_str1_,_str2_);  
```

---

#### Test 简单示例

测试宏`TEST`使用规范如下：
```cpp
TEST(TestSuiteName, TestName) {
  ... test body ...
}
```
* `TestSuiteName`：测试组名，最终报告是以测试组是否全部通过为准；
* `TestName`：当前测试用例名，报告会指出测试组名下当前测试哪个没通过（**必须唯一**）；
* 最终测试结果为 `TestSuiteName.TestName` 方式在 `RUN` 中输出；

【注意事项】：
* 在不同的代码段中测试组名是可以相同的，但 **当前测试用例名** 绝对不能相同，否则编译会报错；
* 两个测试名不能为字符串，否则编译报错；

测试文件（没有`main` 函数），
```cpp
#include "gtest/gtest.h"

// 两个数相加
int add(int a, int b){
    return a + b;
};

// 数字阶乘
int Factorial(int a){
    int result = 1;
    for(int i = 1; i <= a; ++i){
        result *= i;
    };
    return result;
};

// 判断正负号
bool IsPositive(int num) {
    return num > 0? true : false;
}

// Official Example
TEST(HelloTest, BasicAssertions) {
    // Expect two strings not to be equal.
    EXPECT_STRNE("hello", "world");
    // Expect equality.
    EXPECT_EQ(7 * 6, 42);
}

// 自定义测试案例
TEST(myAddTest, add){
    EXPECT_EQ(add(1,3), 4); // expect equal

    EXPECT_LT(add(1,2), 5); // expect less than
    EXPECT_LE(add(1,3), 8); // expect less equal
    EXPECT_GT(add(2,3), 1); // expect greater than
    EXPECT_GE(add(3,3), 6); // expect greater equal

	EXPECT_TRUE(IsPositive(1));     // expect true
	EXPECT_FALSE(IsPositive(-1));   // expect false
};
```

对应的`CMakeLists.txt` 文件如下：
```cmake
cmake_minimum_required(VERSION 3.14)
project(demo)

# GoogleTest requires at least C++14
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 使用include+FetchContent方式从远端拉取第三方库
include(FetchContent)
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/03597a01ee50ed33e9dfd640b249b4be3799d395.zip
)
# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)

enable_testing()
add_executable(
    ${PROJECT_NAME}
    test.cpp
)
target_link_libraries(
    ${PROJECT_NAME}
    GTest::gtest_main
)

include(GoogleTest)
gtest_discover_tests(${PROJECT_NAME})
```

运行并编译：
```shell
$ cmake -B build
$ cmake --build build
$ ./build demo

[==========] Running 2 tests from 2 test suites.
[----------] Global test environment set-up.
# 执行第一个测试组
[----------] 1 test from HelloTest
[ RUN      ] HelloTest.BasicAssertions
[       OK ] HelloTest.BasicAssertions (0 ms)
[----------] 1 test from HelloTest (0 ms total)
# 执行第二个测试组
[----------] 1 test from myAddTest
[ RUN      ] myAddTest.add
[       OK ] myAddTest.add (0 ms)
[----------] 1 test from myAddTest (0 ms total)
# 统计全部测试结果
[----------] Global test environment tear-down
[==========] 2 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 2 tests.

```

---

#### TEST 与正常类的示例

定义一个常规的类：
```cpp
class Base{
public:
    Base(const std::string name): m_name(name) {
        printf("Base has been construct with name [%s]\n", m_name.c_str());
    };
    std::string getName(){
        return m_name;
    };
    ~Base(){
        printf("Base has been descturct\n");
    };
private:
    std::string m_name;
};

void getNameFunc(std::shared_ptr<Base> base){
    std::cout << __func__ << " : usercount: " << base.use_count() << std::endl;
    std::cout << __func__ << " : name: " << base->getName() << std::endl;
};
```

构造测试：
```cpp
TEST(Base, createInstance){
    std::unique_ptr<Base> instance = std::make_unique<Base>("SvenBaseUnqiue");
    ASSERT_NE(instance, nullptr);
    instance.reset();
    EXPECT_EQ(instance, nullptr);
};
```

获取成员变量测试：
```cpp
TEST(Base, getName){
    std::unique_ptr<Base> instance = std::make_unique<Base>("BaseUnique");
    ASSERT_NE(instance, nullptr);
    auto name = instance->getName();
    EXPECT_STREQ(name.c_str(), "BaseUnique");
    instance.reset();
    EXPECT_EQ(instance, nullptr);
};
```

shared_ptr 测试：
```cpp
TEST(Base, shared_ptr){
    std::shared_ptr<Base> instance = std::make_shared<Base>("BaseShared");
    ASSERT_NE(instance, nullptr);
    EXPECT_EQ(1, instance.use_count());
    getNameFunc(instance);
    EXPECT_EQ(1, instance.use_count());
};
```

unique_ptr 测试：
```cpp
TEST(Base, unique_ptr){
    std::unique_ptr<Base> instance = std::make_unique<Base>("BaseUinuque");
    ASSERT_NE(instance, nullptr);
    getNameFunc(std::move(instance));
    EXPECT_EQ(instance, nullptr);
};
```

---
#### TEST_F 简单示例

Test Fixture 目的是使用 **同一批数据** 测试不同行为，如果使用TEST的话是给每一份测试创建一份数据，TEST_F是所有测试共用一份数据，能够避免重复拷贝。

```cpp
TEST_F(TestFixtureName, TestName) {
  ... test body ...
}
```

* `TestFixtureName`: 测试夹具
* `TestName`: 当前测试用例名

TestFixtureName 对应一个 test fixture 类的名称。因此我们需要自己去定义一个这样的类，并让它继承`testing::Test`类，然后根据我们的需要实现下面这两个虚函数：

- `virtual void SetUp()`：类似于构造函数，在 `TEST_F` 之前运行；
- `virtual void TearDown()`：类似于析构函数，在 `TEST_F` 之后运行。

此外`testing::Test`还提供了两个 static 函数：

- `static void SetUpTestSuite()`：在第一个 `TEST` 之前运行
- `static void TearDownTestSuite()`：在最后一个 `TEST` 之后运行

除了这两种，还有一种全局事件，即继承`testing::Environment`，并实现下面两个虚函数：

- `virtual void SetUp()`：在所有用例之前运行；
- `virtual void TearDown()`：在所有用例之后运行。

定义一个待测试的对象`Foo`
```cpp
class Foo{
    public:
        Foo(int num): _num(num) {};

        void Add(int n){
            _num += n;
        };
        void Minus(int n){
            _num -= n;
        };
        int GetNum(){
            return _num;
        };

private:
        int _num;
};
```

定义一个测试者对象 `FooTester`
```cpp
class FooTester: public testing::Test{
    protected:
        Foo * foo;
        void SetUp(){
	        // 这个函数相当于环境重置，在每次运行单个测试前重新将自身构造了一次
            foo = new Foo(1);
            printf("FooTester SetUp called\n");
        };
        void TearDown(){
	        // 这个函数相当于环境回收，在每次运行单个测试后重新将自身析构一次
            delete foo;
            printf("FooTester TearDown called\n");
        };
};
```

定义测试宏
```cpp
TEST_F(FooTester, test_add){
    EXPECT_EQ(foo->GetNum(), 1);
    foo->Add(1);
    EXPECT_EQ(foo->GetNum(), 2);
};

TEST_F(FooTester, test_minus){
    EXPECT_EQ(foo->GetNum(), 1);
    foo->Minus(1);
    EXPECT_EQ(foo->GetNum(), 0);
};
```

定义主函数（这部分可有可无）
```cpp
int main(int argc,  char** argv){
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
};
```

构建并执行：
```shell
$ cmake -B build
$ cmake --build build

$ ./build/demo
Running main() from /Users/gaohao/Desktop/exercise/src/google_test/build/_deps/googletest-src/googletest/src/gtest_main.cc
# Initialize
[==========] Running 2 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 2 tests from FooTester
# 测试用例 Add
[ RUN      ] FooTester.test_add
FooTester Setup called
FooTester TearDown called
[       OK ] FooTester.test_add (0 ms)
# 测试用例 Minus
[ RUN      ] FooTester.test_minus
FooTester Setup called
FooTester TearDown called
[       OK ] FooTester.test_minus (0 ms)
[----------] 2 tests from FooTester (0 ms total)
# Summary
[----------] Global test environment tear-down
[==========] 2 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 2 tests.
```

---

#### TEST_F 与正常类的示例

定义一个能够修改成员变量的对象：
```cpp
class Base{
    public:
        Base(const std::string name): m_name(name) {
            printf("Base has been construct with name [%s]\n", m_name.c_str());
        };
        std::string getName(){
            return m_name;
        };
        // 增加了一个设置名字的成员函数
        void setName(const std::string& new_name){
            m_name = std::string(new_name);
        };
        ~Base(){
            printf("Base has been descturct\n");
        };
    private:
        std::string m_name;
};
```

定义测试对象
```cpp
class BaseTester: public testing::Test{
    protected:
        void SetUp() override{
            m_base = std::make_shared<Base>("SvenBaseTest");
        };
        void TearDown() override{
            m_base.reset();
        };

        std::shared_ptr<Base> m_base;
};
```

构造测试：
```cpp
TEST_F(BaseTester, createInstance){
    std::unique_ptr<Base> instance = std::make_unique<Base>("SvenBaseUnique");
    ASSERT_NE(instance, nullptr);
    instance.reset();
    EXPECT_EQ(instance, nullptr);
};
```

获取名字测试：
```cpp
TEST_F(BaseTester, getName){
    auto name = m_base->getName();
    EXPECT_STREQ(name.c_str(), "SvenBaseTest");
};
```

设置名字测试：
```cpp
TEST_F(BaseTester, setName){
    m_base->setName("NewSvenBase");
    auto name = m_base->getName();
    EXPECT_STREQ(name.c_str(), "NewSvenBase");   // 此处一定要使用EXPECT_STREQ
    printf("name=%s\n", name.c_str());
};
```

在上面的代码中需要注意，对比字符串内容时使用 `EXPECT_STREQ` 或 `ASSERT_STREQ` ，否则对于常量字符串就会对比不同的地址值，即遍字符串内容一致也存在地址不一致的情况。

