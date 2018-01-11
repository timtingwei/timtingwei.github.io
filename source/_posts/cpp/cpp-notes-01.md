---
title: C++ Zero To One 0.001
date: 2018-01-10 06:45:50
tags: [C++, 位操作, new, delete, 运算符重载, 函数指针, 函数对象]
---

接下来, 准备archive每天实际编程中遇到的C++问题，解决问题的过程， 以及得到的结果。以此来熟悉C++的各种特性。

这样做或许会造成知识点离散, 因此, 后续有必要有选择的，对某些特性进行深入剖析。先破破的来吧，以致于我不会从一个细节陷阱中出来，又陷入另一个。


## 右移，左移
```
_capacity >>= 1;     // _capacity *= 0.5;
_capacity <<= 1;     // _capacity *= 2;
```
## new delete
要先new一个新的, 才能delete旧的
```cpp
template <typename T>
void Vector<T>::shrunk() {
  if (_size > (_capacity/2)) return;   // 规模大于1/2不必缩容
  _capacity = std::max(_capacity, DEAFAULT_CAPACITY);
  // 储存一份旧元素, 创建新的数据空间
  T* old_elem = _elem; _elem = new T[_capacity >>= 1];
  for (Rank r = 0; r < _size; r++) {
    _elem[r] = old_elem[r];
  }
  // 删除旧元素的内存空间
  delete [] old_elem;
}
```


new分配空间
```cpp
void copyFrom(T* tarr, int lo, int hi) {
    _elem = new T[_capacity = 2*(hi - lo)];
    _size = hi - lo;
    std::cout << "*tarr = " << tarr[1] << std::endl;
    std::cout << "*_elem = " << *_elem << std::endl;
    // for (int i = 0; i < _size; i++) {*(_elem+i) = tarr[i];}
  }

```

```cpp
void copyFrom(T* tarr, int lo, int hi) {
    
    _size = hi - lo;
    std::cout << "*tarr = " << tarr[1] << std::endl;
    std::cout << "*_elem = " << *_elem << std::endl;
    // for (int i = 0; i < _size; i++) {*(_elem+i) = tarr[i];}
  }
```

为什么一定要为_elem分配空间? 内存回收问题。 在C++ Primer中具体学习

## 同一if语句, 同一行中的后置递增
```cpp
void f() {
  std::cout << "---- test in same if statement----" << std::endl;
  int i = 1;
  int arr[] = {0, 1, 2, 3, 4};
  // 在同if, for, while中, 对某一变量i后置递增, i前后不变
  if (equal(i++, arr[i])) std::cout << "equal\n"
                                    << "now i = " << i << std::endl;
}
```
同一if, for, while语句中, 后置递增返回当前值
同一行, 不同语句, 当然会让值产生变化
## 重载后置++操作符号
前置递增 operator++()
后置递增, 以前置递增为基础 operator++(int i)
increment_and_decrement_operators.cpp

递增运算符重载的返回类型:
`为什么返回*_elem 而不是 *this??`
递减运算符重载函数的返回类型是int&
\*this的类型为Vector<int>, 而*_elem 的类型是int

```cpp
template <typename T>
T& Vector<T>::operator--() {          // 重载前置--操作符
  for (int i = 0; i < _size; i++)
    _elem[i]--;                    // 对每个元素-1
  return *_elem;                    // 返回当前 *this or *_elem
}

template <typename T>
T* Vector<T>::operator--(int i) {          // 重载后置--操作符
  T* e = _elem;
  --*this;                         // 调用前置递减
  return e;
}
```

`<<C++ Primer 5th>> P502, P421`


## 函数指针 和 函数对象

对无序向量的遍历操作中, 统一对各个元素分别实施visit操作

**函数指针**, 只读或者<span style="color:red">**局部性修改**</span>

```cpp
template <typename T>
void Vector<T>::traverse(void (*visit)(T&))      // 函数指针
  {for (int i = 0; i < _size; i++) visit(_elem[i]);}

```


**函数对象**, 可<span style="color:red">**全局性修改**</span>

```cpp
template <typename T> <template VST>
void Vector<T>::traverse(VST & visit) {        // 函数对象
  for (int i = 0; i < _size; i++) visit(_elem[i];)
}
```


### <span style="color:red">**函数指针**</span>

```cpp
int getLarger(const int& i1, const int& i2) {
  return i1 < i2 ? i2: i1;            // 返回两者较大
}
```

#### 声明定义
声明和定义分离:
```cpp
int (*pf1)(const int&, const int&);

pf1 = getLarget;
pf1 = &getLarget;       // 等价,
```

个人认为&表达出pf1是个指针的语义明确点。

声明并定义:
```cpp
int (*pf1)(const int&, const int&) = &getLarger;  
```

#### 使用
使用
```cpp
int i_pf1 = (*pf1)(3, 5);
int i_pf1_2 = (pf1)(3, 5);           // 等价
int i_pf1_3 = getLarget(3, 5);       // 等价 调用原函数
```


#### 赋值, 指向新的函数?
```cpp
bool compareInt(const int& i1, const int& i2) {
  return i1 < i2 ? true : false;      // i1 < i2, 返回true
}

// 声明并定义
bool (*pf)(const int&, const int&) = &compareInt;

// 赋nullptr:指针没有指向任何一个函数
pf = 0;
pf = getLarger;     // error: pf已经被声明成bool(*)(const int&, const int&)
pf = increment;     // error: 同上
```

能重新指向一个nullptr或0, 但是不能指向一个与声明类型不同的新函数地址。

`但如果是一个类型相同的函数, 便可以重新指向该函数。`

```cpp
bool otherCompare(const int& i1, const int&) {return 0;}

pf = otherCompare;
```

#### 重载函数的指针

函数指针 指向重载函数, 需要在声明时, 形参列表和返回类型需要完全匹配

```cpp
void ff(int*);
void ff(unsigned int);

// 重载
void (*pff1)(int *) = &ff;      // pff1指向ff的 void (int*)版本

void (*pff2)(int) = &ff;        // Error
int (*pff3)(unsigned) = &ff;    // Error
```


#### 函数指针作为形参(调用函数指针)

```cpp
// 第三个参数是函数指针类型
void useBigger(const int&, const int&, bool (*pf)(const int&, const int&));

// 调用时, 传入指向compareInt的指针
useBigger(i1, i2, &compareInt);
```


可以用别名的方法简化定义

```cpp
typedef decltype(compareInt) *FuncP2;    // FuncP2是指向函数的指针类型
typedef decltype(compareInt) Func;       // decltype()返回函数类型, Func是函数类型

void useBigger(const int&, const int&, FuncP2);
void useBigger(const int&, const int&, Func2);   // 编译器将Func2函数类型自动转换为了函数的指针类型
```


#### 返回指向函数的指针

一般, 别名

```cpp
using PF = bool(*)(const int&, const int&);      // PF是函数的指针类型
using F = bool(const int&, const int&);          // F是指针类型

PF f1(int);          // 返回函数的指针类型
F *f1(int);          // 显式f1返回的是一个指针类型
F f2(int);           // Error:F是函数类型, 无法返回一个函数

```


尾置
```cpp
auto f1(int) -> int(*)(int*, int)
```

### <span style="color:red">**函数对象**</span>

#### 重载()
说明这个对象, 他可以当作函数来使用。因此需要重载"()"


```cpp
// 定义一个函数对象
class Sum{
 public:
  int sum = 0;
  void operator()(int iarr[], int n) {
    for (int i = 0; i < n; i++)
      sum += iarr[i];
    std::cout << "sum = " << sum << std::endl;
  }
};
```

#### 使用这个函数对象

```cpp
Sum s;
int iarr[] = {1, 2, 3, 4, 5, 6};
int n = 6;
s(iarr, n);      // sum = 21
```

#### 作为其他函数的参数

```cpp
// 函数对象作为另一个函数的形参
template <typename CLS>
void f(CLS & c) {
  int iarr[] = {3, 6, 9};
  int n = 3;
  c(iarr, n);
}


// 调用
Sum s1;
f(s1);       // sum = 18
```

#### 回顾一下数据结构中的用法



```cpp
template <typename T> template <typename VST>
void Vector<T>::traverse(VST visit) {
  // 对每个元素执行visit操作
  for (int i = 0; i < _size; i++) {visit(_elem[i]);}
}

// 为什么不能用引用类型?
// no known conversion for argument 1 from ‘Increase<int>’ to ‘Increase<int>&’
```

```cpp
/* my error test
template <typename T>
void increase(Vector<T> V) {
  V.traverse(Increase(T& e));
}
*/
template <typename T>
void increase(Vector<T> V) {
  V.traverse(Increase<T>());
}

// 为什么不用实例化, 带入参数便可直接调用??
```
