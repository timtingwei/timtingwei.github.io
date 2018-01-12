---
title: C++ Zero To One 0.003
date: 2018-01-11 16:06:51
tags: [C++, 继承, 类, virtual, override, 函数重载, static, 静态成员, ]
---

# 重载函数什么时候用virtual?
源码: [source code - load_funtion.cpp](https://github.com/timtingwei/cppd/blob/master/tmp/overload_function.cpp)
## 问题来源
```cpp
  template <typename VST> void traverse(VST visit);
  // template <typename VST> virtual void traverse(VST visit, T* e);
  template <typename VST> void traverse(VST visit, T* e);
```

为什么这里不需要virtual??

```cpp
// 单个T类型元素加1的类
template <typename T>
struct Increase {
  virtual void operator()(T &e) {e++;}    // Increase<T>()实现函数功能  
};
```

为什么这里又需要virtual??

## 概念偏差
virtual 和我原先理解的重载函数不太相同。也就是vitual和重载函数不是同一个概念。

**vitual为虚函数**：基类的引用或者指针调用一个虚成员函数时会执行动态绑定。 

**重载函数**：同一作用域内的几个函数名字相同但形参列表不同。
## 使用与不使用virtual

解释一下 virtual 和 override 搭配, 继承类中可对基类的虚函数重载
```cpp
struct Base {    // 声明virtual后, 可被继承类override重载
  virtual void print() {std::cout << "Base\n";}
};

struct Derived : Base {   // 'override'可写可不写
  void print() override {std::cout << "Derived\n";}
};
```


```cpp
struct Base {
  void print() {std::cout << "Base\n";}
};

struct Derived : Base {
  void print() {std::cout << "Derived\n";}
};
```

`关键问题: 上述两者什么差别????`

运行测试一下:
```cpp
// 注释是使用virtual结果
  Base b;
  Derived d;

  Base& br = b;      // refer b, br的类型是Base&
  Base& dr = d;      // dr类型也是Base&
  br.print();        // Base
  dr.print();        // Derived

  Base* pb = &b;     // pointer to b, Base*
  Base* pd = &d;     // Base* as well

  pb->print();       // Base
  pd->print();       // Derived

  b.Base::print();   // Base
  d.Base::print();   // Base

```
使用vitual的运行结果:

```sh
Base
Derived
Base
Derived
Base
Base
```

不使用虚方法的运行结果: 
```sh
Base
Base
Base
Base
Base
Base
```

virtual版本, 基类的引用或者指针会在调用一个虚函数时候, 动态绑定对应的函数。

## 解决最初问题
### 为什么traverse函数不需要virtual
traverse函数，属于在同一作用域，形参列表不同的函数。重载的概念是函数重载的概念，并不是继承类之间的虚函数override概念。
### 为什么函数对象需要virtual重载操作符函数?

定义继承类Increase_two, override在基类中被声明成virtual的类, 实现元素加2
```cpp
// 函数对象用virtual声明operator()()
struct Increase {
  // 重载()后, 对象可充当函数的功能
  virtual void operator()(int* e) {(*e)++;}
};

struct Increase_two : Increase {
  void operator()(int* e) override {(*e)+= 2;}    // 元素+2;
};
```

测试函数, 以及prints结果如下：
```cpp
void f_increase() {
  Increase a;
  Increase_two b;

  Increase& ra = a;
  Increase& rb = b;     // rb的类型为 Increase&

  int e = 5; int* pe = &e;
  ra.operator()(pe);
  std::cout << "e = " << e << std::endl;    // prints "e = 6"
  int e2 = 5; int* pe2 = &e2;
  rb.operator()(pe2);   // 调用基类的引用时, 动态绑定到Increase_two中的方法
  std::cout << "e2 = " << e2 << std::endl;  // prints "e2 = 7"
}
```

对重载操作符声明virtual, 可以让继承类对他的重载操作符行为进行重载。当调用基类的引用或者指针时候, 在编译阶段就能绑定到继承类对应的重载函数版本。



# 静态成员声明定义使用
源码: [source code - static.cpp](https://github.com/timtingwei/cppd/blob/master/tmp/static.cpp)
## 静态成员的特点
静态数据成员不属于任何类的一个对象。

类似于全局变量, 存在于任何的函数定义之外, 一直存在于整个程序的生命周期
## 类内static成员如何声明定义?
> * 在类内部声明, 外部通过类名作用域访问定义
```cpp
{
class MyStatic
public:
 static int i_stc;   // 类内部声明
 // ...
}

int MyStatic::i_stc = 0;     // 类外部定义, 注意需要声明成员的类型
```

也可以通过静态成员函数和一般的成员函数对它进行修改, 此时静态成员函数可通过类名访问。
## 在不同实例中被多次调用改变后的情况?

```
void MyStatic::increment() {++i_stc;}

void f() {
  MyStatic c0;
  c0.increment();
  std::cout << "c0 = " << c0.i_stc << std::endl;
  MyStatic c1;
  c1.increment();
  std::cout << "c1 = " << c1.i_stc << std::endl;
}

void f1() {
  MyStatic c2;
  c2.increment();
  std::cout << "c2 = " << c2.i_stc << std::endl;
}

```

```sh
c0 = 1
c1 = 2
c2 = 3
```

静态数据成员类似于全局变量, 无论在何处, 每调用一次修改静态成员的函数, 数据相应的改变, 且不会随着作用域的离开而销毁。


