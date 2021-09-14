# C++基础

- 引用和指针的区别

指针是一个变量，存储地址，指向内存存储单元。

引用是原变量的别名，和原变量是一个东西。

- 函数中父类为形参，子类为实参，并且形参是引用

在函数中调用父类的虚函数，在主函数中传入子类，最后调用的是子类的函数

```c++
#include<iostream>
using namespace std;

class Fish {
    public:
    virtual void show() {
        cout << "fish" << endl;
    }
};

class Carp: public Fish {
    public:
    void show() {
        cout << "carp" << endl;
    }
};

void print(Fish& fish) {
    fish.show();
}

int main() {
    Carp carp;
    print(carp);
    return 0;
}
```

- 拷贝构造函数

## 类相关

