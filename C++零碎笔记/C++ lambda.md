
link: https://blog.csdn.net/weixin_52345097/article/details/131499309

lambda表达式的引入是为了简化程序并衔接现代代码逻辑，**减少函数跳转，增强函数可读性**。

---
#### 方式一：仿函数方式

定义一个标准结构体
```cpp
struct Goods{
	std::string _name;
    double _price;
    double _evaluate;

    Goods(const char* str, double price, double evaluate): _name(str), _price(price), _evaluate(evaluate) {
        printf("Goods [%s] price=%.3lf, evaluate=%.3lf has been construct\n", _name.c_str(), _price, _evaluate);
    };

    void print(){
        printf("Goods [%s] price=%.3lf, evaluate=%.3lf has been printed\n", _name.c_str(), _price, _evaluate);
    };
};

void printGoods(std::vector<Goods> & v){
    for(auto & iter: v){
        iter.print();
    };
    printf("All goods print done, total %d\n", v.size());
};
```

定义一个用于排序的对象
```cpp
struct ComparePriceLess{
    bool operator() (const Goods& gl, const Goods& gr){
        return gl._price < gr._price;
    };
};

struct ComparePriceGreate{
    bool operator() (const Goods& gl, const Goods& gr){
        return gl._price > gr._price;
    };
};
```

测试函数
```cpp
struct ComparePriceLess{
    bool operator() (const Goods& gl, const Goods& gr){
        return gl._price < gr._price;
    };
};

struct ComparePriceGreate{
    bool operator() (const Goods& gl, const Goods& gr){
        return gl._price > gr._price;
    };
};
```

```cpp
void CompareWithFakeFunction(){
    printf("Fake fucntion\n");
    std::vector<Goods> v = { 
        {"Apple", 2.1, 5.0},
        {"Banana", 3, 4.0},
        {"Origin", 2.2, 3.0}
    };
    ComparePriceLess compgl;
    ComparePriceGreate compgr;

    printf("======================================\n");
    printf("Rerady to compare greater\n");
    std::sort(v.begin(), v.end(), compgl);
    printGoods(v);

    printf("======================================\n");
    printf("Rerady to compare lesser\n");
    std::sort(v.begin(), v.end(), compgr);
    printGoods(v);

    printf("All test done\n");
};

```

使用这种方法就需要至少执行两次跳转才能知道`ComparePriceLess`和`ComparePriceGrater`的含义。

---

#### 方式二：lambda表达式

```cpp
void CompareWithLambdaFunction(){
    printf("Lambda functoin\n");
    std::vector<Goods> v = { 
        {"Apple", 2.1, 5.0},
        {"Banana", 3, 4.0},
        {"Origin", 2.2, 3.0}
    };
    ComparePriceLess compgl;
    ComparePriceGreate compgr;

    printf("======================================\n");
    printf("Rerady to compare greater\n");
    // 这两种方式都可以
    // std::sort(v.begin(), v.end(), [](const Goods& gl, const Goods& gr) -> bool{return gl._price > gr._price;});
    std::sort(v.begin(), v.end(), [](const Goods& gl, const Goods& gr) {return gl._price > gr._price;});
    printGoods(v);

    printf("======================================\n");
    printf("Rerady to compare lesser\n");
    // std::sort(v.begin(), v.end(), [](const Goods& gl, const Goods& gr) -> bool{return gl._price < gr._price;});
    std::sort(v.begin(), v.end(), [](const Goods& gl, const Goods& gr) {return gl._price < gr._price;});
    printGoods(v);

    printf("All test done\n");
};


```