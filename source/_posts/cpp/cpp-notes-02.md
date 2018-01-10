---
title: 持续性练习C++-02
date: 2018-01-10 13:01:23
tags: [C++, 指针, 数组]
---

# 数组, 指针数组, 指向指针的指针的数组, ....
在逛[Cplusplus Forum - Beginner](http://www.cplusplus.com/forum/beginner/)的时候发现了一个看上去看简单的[问题](http://www.cplusplus.com/forum/beginner/228196/)

**How do I receive two numbers from the keyboard, save them in an array of two element?**

## **第一次尝试**
```cpp
void f0() {     // ERROR: 先存放再输入不会修改数组中的数据
  int a, b;
  int iarr[2] = {a, b};
  std::cin >> a >> b;   // 不合理:先存放, 再输入
  std::cout << iarr[0] << ' ' << iarr[1] << std::endl;
  // 4197661 0
}
```

我想得太简单了。不能在标准输入中, 直接对数据进行修改。

## **下面给出的答案**

1, 
```cpp
void f1() {
  int iarr[2] = {};
  std::cin >> iarr[0] >> iarr[1];     // 直接输入到数组中
}
```

2, 
```cpp
void f2() {
  int a, b;
  std::cin >> a >> b;     // 先输入, 再存放
  int iarr[2] = {a, b};
}
```

## **但是我仍然考虑我思路实现的可能性？**

对已经放置在数组中的值进行修改的思路如何实现？

### 第一个想到的就是指针。

```cpp
void f3() {  // BUG: 为什么不能通过数组中保存的指针修改数组??
  int *pa, *pb;
  int* iarr[2] = {pa, pb};
  int tmp_a, tmp_b;
  std::cin >> tmp_a >> tmp_b;        // 存放到临时变量
  pa = &tmp_a; pb = &tmp_b;
  std::cout << "*pa = " << *pa << std::endl;    // *pa = 1
  std::cout << *(*iarr) << std::endl;
  std::cout << *(iarr[0]) << ' ' << *(iarr[1]) << std::endl;
}
```

```sh
$ ./a.out
1
2
*pa = 1
Segmentation fault (core dumped)
```

很气, 我得出结论。不能通过数组中保存的指针修改数组。

### 数组作为函数形参
接着, 我想起之前在函数中通过数组改变过值, 便想要实现如下任务：
**创建一个函数, 输入一个保存指向整数的指针的数组, 修改索引n位置的指针所指向的值, 返回**

```cpp
void revise_value_int(int* arr, int n) {
  // 索引为n指向的整数递增1
  (*(arr+n)) += 1;
}

void call_revise() {
  int iarr[] = {0, 1, 2};
  revise_value_int(iarr, 2);
  std::cout << "iarr[2] = " << iarr[2] << std::endl;
}

call_revise();
```
可以改变。但是仔细一想, 我仅仅只是在数组中存放了整数。

### 函数形参为存放指针的数组

接着，我改变了数组，用于存放指针
```cpp
int* pa, *pb, *pc;
int ptrArr[] = {pa, pb, pc};
```

编译时就出现了错误,.*error: invalid conversion from ‘int*’ to ‘int’ [-fpermissive]*
**因为现在数组保存着的是指向整型的指针, 因此数组的类型应该是 int\***



修改
```cpp
int* pa, *pb, *pc;
int* ptrArr[] = {pa, pb, pc};
```

写修改指针所指向整数的函数, 注意, 这个形参类型是`int**`, **ptrArr是指向指针的指针, 调用数组, 实际上是调用指向数组首元素的指针。**
```cpp
void revise_value_ptr(int** ptrArr, int n) {
  // 索引为n的指向的指针所指向的整数递增1
  (**(ptrArr+n))+=1;
}
```

### Segmentation fault (core dumped) 
再次编译
```sh
Segmentation fault (core dumped)
```


出现 Segmentation fault (core dumped) 的原因， 参考[http://blog.sina.com.cn/s/blog_4b9eab320100uivj.html]
#### 1 内存访问越界

  a) 由于使用错误的下标，导致数组访问越界

  b) 搜索字符串时，依靠字符串结束符来判断字符串是否结束，但是字符串没有正常的使用结束符

  c) 使用strcpy, strcat, sprintf, strcmp, strcasecmp等字符串操作函数，将目标字符串读/写爆。应该使用strncpy, strlcpy, strncat, strlcat, snprintf, strncmp, strncasecmp等函数防止读写越界。
 

#### 2 多线程程序使用了线程不安全的函数。

#### 3 多线程读写的数据未加锁保护。

对于会被多个线程同时访问的全局数据，应该注意加锁保护，否则很容易造成core dump

 

#### 非法指针
  a) 使用空指针
  b) 随意使用指针转换。一个指向一段内存的指针，除非确定这段内存原先就分配为某种结构或类型，或者这种结构或类型的数组，否则不要将它转换为这种结构或类型的指针，而应该将这段内存拷贝到一个这种结构或类型中，再访问这个结构或类型。这是因为如果这段内存的开始地址不是按照这种结构或类型对齐的，那么访问它时就很容易因为bus error而core dump.

#### 堆栈溢出

不要使用大的局部变量（因为局部变量都分配在栈上），这样容易造成堆栈溢出，破坏系统的栈和堆结构，导致出现莫名其妙的错误。

### 非法指针
我的代码是:
```cpp
int* pa, *pb, *pc;
int* ptrArr[] = {pa, pb, pc};
```
这里我使用了非法的指针, 这个指针没有指向任何

修改,


```cpp
void call_revise() {
  int a = 1, b = 2, c = 3;
  int* pa = &a, *pb = &b, *pc = &c;
  int* ptrArr[] = {pa, pb, pc};
  another_revise_value_ptr(ptrArr, 2);
  std::cout << "**(ptrArr)   = " << **(ptrArr)   << '\n'
            << "**(ptrArr+1) = " << **(ptrArr+1) << '\n'
            << "**(ptrArr+2) = " << **(ptrArr+2) << std::endl;
}

call_revise()
```
编译得到
```sh
**(ptrArr)   = 1
**(ptrArr+1) = 2
**(ptrArr+2) = 4
```

### 解决最开始问题并总结

**修改后的代码**
```cpp
void f4() {     // DEBUG: 先存放再输入不会修改数组中的数据
  int a, b;
  int* pa = &a, int* pb = &b;     // 注意: 不要定义空指针
  int* iarr[2] = {pa, pb};        // 注意数组的类型是数组中元素的类型
  std::cin >> a >> b;   // 也能合理:先存放, 再输入
  std::cout << **iarr << ' ' << **(iarr+1) << std::endl; // 解引用
  // 4197661 0
}
```

#### 不要定义空指针

如这样 
```cpp
int* pa;
```
即使要定义也要
```
int *pa = 0;
int *pb = nullptr;
```
尽量让指针指向某个对象
```cpp
int *pa = &a;
```

#### 数组的类型 
数组的类型是数组中元素的类型
```cpp
int* iarr[2] = {pa, pb};        // 类型为 int*
```

#### 解引用数组

```cpp
int* iarr[2] = {pa, pb};        // 如果数组这样定义
iarr;                           // iarr是指向数组首个元素的指针
*iarr;                          // 因为pa是指针, 所以解引用数组头指针, 得到pa地址
**iarr;                         // 再次解引用得到pa指向的int
```

标准输出它们得到
```sh
iarr = 0x7ffed5caa560
*iarr = 0x7ffed5caa548
**iarr = 1
```


## **更多的尝试**

### 数组中存放对象
```cpp
template <typename T>
class Name {
  char* _c; int _length; int _capacity;
 public:
  Name(char* c, int length): _c(c), _length(length) {}
  char* getName() const;
  int getLength() const;
};

template <typename T>
char* Name<T>::getName() const {
  return _c;
}

template <typename T>
int Name<T>::getLength() const {
  return _length;
}



void class_array() {
  char c1[] = {'t', 'i', 'm'};
  char c2[] = {'h', 'u'};
  int length1 = 3, length2 = 2;
  Name<int> name1(c1, length1), name2(c2, length2);
  Name<int> nArr[] = {name1, name2};
  int size = 2;
  print_instance_data_in_array(nArr, size);
}

void f() {
  char c[] = {'t', 'i', 'm'};
  int length = 3;
  Name<int> n(c, length);
}

void print_instance_data_in_array(Name<int>* nArr, int size) {
  for (int i = 0; i < size; i++) {
    std::cout << "Name = ";
    for (int j = 0; j < nArr[i].getLength(); j++) {
      std::cout << *((nArr[i].getName())+j);
    }
    std::cout << "\n";
  }
}
```

```sh
Name = tim
Name = hu
```

面向对象可以让我从无尽的指针中抽身出来, 关注相互之间的联系。

### 数组中存放长度不同的数组

```cpp
void f_array() {
  int a = 3, b = 5, c = 7;
  int* pa = &a, *pb = &b, *pc = &c;
  int _size_pa = 1, _size_pb = 1, _size_pc = 1;
  int* pArr1[] = {pa};    // pArr1数组存放pa, pb指针
  int _size_pArr1 = 1;
  int* pArr2[] = {pa, pb, pc};    // pArr2数组存放pa, pb, pc指针
  int _size_pArr2 = 3;
  int* pArr3[] = {pa, pc};
  int _size_pArr3 = 2;
  int size_pArr[] = {1, 3, 2};
  int** ppArr[] = {pArr1, pArr2, pArr3};   // ppArr存放3个指针数组
  int _size_ppArr = 3;
  int size_ppArr[] = {3};
  int* size_arr[] = {size_ppArr, size_pArr};
  // convert_size_structor(size_arr);
  print_arr_b(ppArr);
}

void print_arr_b(int*** ppArr) {
  // 解引用并打印ppArr数组中依次数组中的pa, pb, pc
  std::cout << "a0 = " << ***ppArr<< " in pArr1[0]" << '\n'
            << "b0 = " << **(*(ppArr+1)+1)<< " in pArr2[1]" << '\n'
            << "c0 = " << **(*(ppArr+2)+1)<< " in pArr3[1]" << '\n'
            << std::endl;
}
```

对于简单的数组, 打印储存不同长度数组的数组, 需要更多的数据结构知识, 而且不同的算法还有好坏, 可见数据结构的重要性. 这部分的打印实现, 留作日后我数据结构解引用后的小试牛刀。
## **感想**
即使从一个很小很简单的问题入手, 也能对自己有所提高, 计算机科学没有简单, 复杂是由简单构成的, 计算机科学承认复杂, 计算机科学尝试破译复杂。
