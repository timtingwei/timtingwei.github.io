---
title: C++ Zero To One 0.004
date: 2018-01-12 10:28:06
tags: [C++]
---


```
# 1, bool和int的隐式转换
	[cppd - bool_int_convert](https://github.com/timtingwei/cppd/blob/master/tmp/bool_int_convert.cpp)
	
	>* 问题起因
		======================= source code =========================
		template <typename T>
		int Vector<T>::disordered() const {
		int count = 0;   // 计数器
		for (int i = i; i < _size; i++)
			count += (_elem[i-1] > _elem[i]);    // 逆序对则计数+1, bool和int的隐式转换
		return count;
		}
		=============================================================
		数据结构学习中, 一次循环将布尔值作为右值递增左值计数器。
	
	>* sizeof(bool) = 1
  
	>* bool-> not bool
		布尔值转赋值给非布尔值, 初始值false时0, true时1 	
	
	>* not bool -> bool
		非布尔值转换成布尔值, 初始值非0为true, 初始值为0和空指针为false;
	
		其他都没什么问题, 值得注意的是, 引用和指针
		=================== source code =============================
		void f_to_bool() {
		int i1 = 5, i2 = 0;
		int* pi0 = 0;       // 定义空指针
		int* pi1 = &i1;      // 指针指向i1
		int& ri0 = i2;      // 引用i2, i2的值为0
		int& ri1 = i1;      // 引用i1
		
		bool bi = i1;        // prints  bi = 1
		bool bpi0 = pi0;     // prints  bpi0 = 0
		bool bpi1 = pi1;     //         bpi1 = 1
		bool bri0 = ri0;     //         bri0 = 0
		bool bri1 = ri1;     //         bri1 = 1
		=============================================================
----------------------------------------------------------------------------------------------------  
# 2, 不能返回函数内部初始化的指针
	[cppd - return_ptr](https://github.com/timtingwei/cppd/blob/master/tmp/return_ptr.cpp)

	>* 问题
		数据结构学习中, Rank* deduplicate_lower();
		我想返回函数内部声明并初始化的指针, 即保存索引的数组, 但结果却出错了。
	>* 错误的方法
		函数体内声明定义的指针，返回不了
		===================== source code ===========================
		int* f() {
			int iarr[] = {};
			iarr[0] = 2;
			return iarr;
		}
		--------------------- ./a.out -----------------------------
		warning: address of local variable ‘iarr’ returned [-Wreturn-local-addr]
		============================================================
		我理解一下，函数内部定义的局部变量会在退出函数体后回收, 即使返回后地址也找不到，更谈不上指向谁。
	
	>* 可行的方法
		将指针作为参数传入
		================== source code =============================
		int* f1(int* iarr) {
			*iarr += 1;
			return iarr;
		}
		===========================================================
		另外一个疑问, 将指针作为参数, 可以在修改指针后返回, 但既然是指针, 还有什么返回的必要??
		便于函数之间的调用, 还是需要的。
	
	>* 另一种可行方法: malloc返回指针
		================= source code ============================
		// 强制返回指针, 分配内存却不释放
		int* fun() {
			int* i_ptr = (int*) malloc(sizeof(int*));    // malloc分配内存
			for (int i = 0; i < sizeof(int*); i++)
				*(i_ptr+i) = 0;
			// free(i_ptr);  // 释放指针
			return i_ptr;    // 函数返回指针
		}
		============================================================
---------------------------------------------------------------------------------------
# 3, malloc和free
	[cppd - malloc_free](https://github.com/timtingwei/cppd/blob/master/tmp/malloc_free.cpp)
  
	> * 概念
		malloc:
		void* malloc (size_t size);
		在语句块中分配一个memeory, 返回一个在作用域中的指针
		free:
		void free(void* ptr);
		重新分配作用域中的memeory, 给未来的分配使用
	
	> * 需要include的库
		#include <stdlib.h>
		#include <stdio.h>
	
	> * 案例
		=================== source code ===========================
		int main() {
			int i, n;
			char* buffer;

	        printf("how long do you want the string?");
			scanf("%d", &i);

	        buffer = (char*) malloc(i+1);
	        if (buffer==NULL) exit(1);

	        for (n=0; n < i; n++)
				buffer[n] = rand()%26 + 'a';
			buffer[i] = '\0';

	        printf("Random string: %s\n", buffer);
	        free(buffer);
		}
		------------------ ./a.out -------------------------------
		how long do you want the string?5
		Random string: nwlrb
		===========================================================
------------------------------------------------------------------------------------------
# 4, 未声明大小的数组
	[cppd - array_size](https://github.com/timtingwei/cppd/blob/master/tmp/array_size.cpp)

	> * 问题
		=================== source code ===========================
		int size = 5;
		int iarr[size] = {};
		------------------- ./a.out ----------------------------------
		error: variable-sized object ‘iarr1’ may not be initialized
		===========================================================
	
	> * const
		=========== source code ==========
		const unsigned int size = 5;
		int iarr1[size] = {};
		==================================
	
	> * define
		========== source code ==============
		#define ARRAY_SIZE 5;
		// #define ARRAY_SIZE = 5;    // ERROR
		int iarr[ARRAY_SIZE] = {}
		======================================
-------------------------------------------------------------------------------------------------
# 5, continue 和 break 区别
	[cppd - continue_break](https://github.com/timtingwei/cppd/blob/master/tmp/continue_break.cpp)
	
	> * 概念 
		continue: 跳过for, range-for, while or do-while, 循环体剩余部分 
		break: 闭合的for, range-for, while, do-while 循环或 switch选择终止
	
	> * continue实例:
		loop:
		=================== source code ============================
		// 跳过loop剩余部分
		for (int i = 0; i < 5; i++) {
			for (int j = 2; j <7; j++) {
				if (j - i == 2) {
				std::cout << "j-i = 2" << std::endl;
				continue;}
				// ...
				std::cout << "after continue " << std::endl;
			}
		}
		============================================================

    > * break实例:	
		1, loop:
		==================== source code ===========================
		// 终止循环
		for (int i = 0; i < 5; i++) {
			for (int j = 2; j <7; j++) {
				if (j - i == 2) {break;}      // 终止内存循环
				std::cout << "j - i = " << j - i << std::endl;
			}
		}
		============================================================
	
		2, switch
		=================== source code ===========================
		int i = 2;
		switch (i) {
			case 1: std::cout << "1\n";
			case 2: std::cout << "2\n";         // 选择到这里
			case 3: std::cout << "3\n";
			case 4:
			case 5: std::cout << "45 \n";
				break;                            // 终止switch
			case 6: std::cout << "6\n";
		}
		------------------- ./a.out ------------------------------
		2
		3
		45
		============================================================
-----------------------------------------------------------------------------------------------------
# if...else 和 if...else if的区别
	[cppd - if_else_elseif](https://github.com/timtingwei/dsacpp/blob/master/src/02/vector_ordered.cpp)
	>* 问题
		==================== source code ==========================
		while (lo < hi) {    // 区间存在
			Rank mi =  (lo + hi) >> 1;        // 取中点
			if      (e < elem[mi]) hi = mi;      // 深入前半段查找
			else if (elem[mi] < e) lo = mi + 1;  // 深入后半段查找
			else                   return mi;    // 在mi命中
		}
		===========================================================
		if, else...if 和 else是什么关系？
		从结果上看, 二分查找, 在前半段找到至少比较1次, 后半段2次。
	
	>* if...else
		====
		int i = 6;
		if (i == 5) {std::cout << "i = 5" << std::endl;
		} else {
			if (i > 5) {std::cout << "i > 5" << std::endl;
			} else { std::cout << "i < 5" << std::endl;}
		}
		--------------------- ./a.out ---------------------------
		i > 5
		===
	>* if...else if...else if
		===
		int i = 4;
		if (i == 5) std::cout << "i = 5" << std::endl;
		else if (i < 5) std::cout << "i < 5" << std::endl;
		else if (i > 5) std::cout << "i > 5" << std::endl;
		-----
		i < 5
		===
	>* if
		===
		int i = 6;
		if (i == 6) std::cout << "i = 6" << std::endl;
		if (i != 7) std::cout << "i != 7" << std::endl;
		----------------- ./a.out --------------------------
		i =  6
		i != 7
		====================================================
	>* 各自的作用域辨析
		==================== source code ==========================
		bool foo = false, bar = true, baz = true;
		if (foo) {
			// <- this block is only executed if 'foo' is true
			std::cout << "if(foo)\n";
		} else if (bar) {  // <- 'bar' is only checked if 'foo' is false
			// <- this block only executed if 'foo' is false and 'bar' is true
			std::cout << "else if(bar)\n";
		} else {
			// <- this block only executed if 'foo' and 'bar' are both false
			std::cout << "else {}\n";
		}
		if (baz) {   // <- no 'else', so previous 'ifs' don't matter
			// <- this block only executed if 'baz' is true.   foo/bar don't matter
			std::cout << "if(baz)\n";
		}
		---------------------- ./a.out ----------------------------------
		// else if(bar)
		// if(baz)
		==============================================================
		我用的是Google cpplint的cpp书写方式, else要在两个反花括号之间。
		这样更容易看到 if, else..if, else 是一个语句块, if又是单独的一个。
	>* 总结
		if(i < n), if...else(n < i), 这两个语句条件除了n = i互补。
		如果有一个true, 则不会执行接下来的else; 
		如果都为false, else执行, 条件相当于(n==i)为true.
		
		因此，对于二分查找来说, 
		前半段找到只执行一次if, 后半段找到执行 if 和 if else两次比较, 还节省一次判等的操作。
		
		1, if...else if...else 可作三个并列关系的选择, 但运行时有先后顺序
		2, if...else 多用 (cond)? expre1 : expre2; 代替
		===== source code ======
		int i = 4, count = 0;
		(i == 4) ? count++
			: i = 4;
		========================
		缺点是执行的语句只能为一个表达式且单行, 也不能return
		3, if, if 语句多为两个condtions没有并列关系。
```
