
link: https://blog.csdn.net/qq_15041569/article/details/130675361

---

 **Mock，更确切地说应该是Mock Object**，当需要进行单元测试、模块接口测试时，如果这个模块依赖另外类或接口，并且依赖的类 **还没有开发好** ，那么可以通过 gMock 模拟这个类的行为，以便让后续的测试顺利完成。

为了实现模拟类行为，gMock提供了一个核心宏：
```cpp
EXPECT_CALL(mock_object, method(matcher1, matcher2, ...))
    .With(multi_argument_matcher)
    .Times(cardinality)
    .InSequence(sequences)
    .After(expectations)
    .WillOnce(action)
    .WillRepeatedly(action)
    .RetiresOnSaturation();
```

* `mock_object`: Mock类的对象；
* `method(matcher1, matcher2, ...)`：Mock类中的某个方法名；
* `Times(cardinality)` ：定义的method运行次数；
* `InSequence(sequences)`： method被执行顺序（优先级）；
* `WillOnce(action)`：调用时所产生的行为，比如定义该方法返回怎么样的值；
* `WillRepeatedly(action)`：缺省/重复行为。

【Note】 `EXPECT_CALL` 中所有方法都是用来 **设置** 类的行为，如果想要获得值则需要实际的 **mock对象调用**；

---

定义一个待实现的类，这个类还没有被具体实现，但后面的测试需要这个类成员函数的结果：
```cpp
class FooInterFace{
    public:
	    // 析构函数必须是虚函数
        virtual ~FooInterFace() {};
    public:
        std::string getAttributeString();
        void setValue(double val);
        int setDoubleValue(int a, int b);
        // 也可以是纯虚函数
        // virtual std::string getAttributeString() = 0;
        // virtual void setValue(double val) = 0;
        // virtual int setDoubleValue(int a, int b) = 0;
};
```

定义一个Mock类，并继承于需要被测试的类：
```cpp
class MockFoo: public FooInterFace{
    public:
        MOCK_METHOD0(getAttributeString, std::string());
        MOCK_METHOD1(setValue, void(double val));
        MOCK_METHOD2(setDoubleValue, int(int a, int b));
};

// Formate
// MOCK_METHODN(function_name, return_value(params1, params2,...));
```

gMock最多支持10个形参的宏定义，宏格式 `MOCK_METHOD+形参数：
* `MOCK_METHOD0`：绑定基类`std::string getAttributeString()`函数，没有传入参数；
* `MOCK_METHOD1`：绑定基类 `void setValue(int a)`函数，传入一个参数；
* `MOCK_METHOD2`：绑定基类 `int setDoubleValue(int a, int b)` 函数，传入两个参数；

写测试部分：
```cpp
int main(int argc, char** argv){
	// 初始化gMock环境
    testing::InitGoogleMock(&argc, argv);
    std::string input_str = "Hello word";
    // 定义一个
    MockFoo mock_foo;

	// 设置 getAttributeString 函数行为
    EXPECT_CALL(
            mock_foo,
            getAttributeString()
    ).Times(1).WillOnce(testing::Return(input_str));
    std::string return_value = mock_foo.getAttributeString();  // 调用getAttributeString 并获得返回值
    std::cout << return_value << std::endl;

	// 设置 setValue 函数行为
    EXPECT_CALL(mock_foo, setValue(testing::_));
    mock_foo.setValue(10);   // 调用 setValue ，但因为基类返回值是void所以不用获取

	// 设置 setDoubleValue 行为
    EXPECT_CALL(
            mock_foo,
            setDoubleValue(10, 20)
    ).Times(1).WillOnce(testing::Return(100));
    int value = mock_foo.setDoubleValue(10, 20);  // 调用 setDoubleValue 并获得返回值
    std::cout << value << std::endl;

    return EXIT_SUCCESS;
}

```

在 `CMakeLists.txt` 文档中添加 `gmock` 部分

```cmake
target_link_libraries(
    ${PROJECT_NAME}
    GTest::gtest_main
    GTest::gmock
)
```

执行：
```shell
$ cmake --build build
$ ./build demo

Hello Word
100
```

---

### 未完待续
