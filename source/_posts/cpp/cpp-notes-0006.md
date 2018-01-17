---
title: C++ Zero To One 0.006
date: 2018-01-16 22:30:14
tags:
---

```
1, 局部变量的销毁顺序
	================= source code ===============================
	void f(int i) {
		Table aa(1);
		Table bb(2);
		if (i > 0) {
			Table cc(3);
		}
		Table dd(4);
	}
	-------------- ./a.out ----------------
	in Table() sz = 1
	in Table() sz = 2
	in Table() sz = 3
	in ~Table() sz = 3        // cc在if退出时析构, 在dd构造之前
	in Table() sz = 4
	in ~Table() sz = 4        // a, b, c构造; c, b, a析构
	in ~Table() sz = 2
	in ~Table() sz = 1
	=============================================================
-------------------------------------------------------------------------------------

2, 构造函数复制

	>* 直接复制
		======= source code ========
		void h() {
			Table t1;
			// Table t2 = t1;    // ERROR: 复制初始化
			Table t3;
			
			// t3 = t2;          // ERROR: 复制赋值
		}
		==============================
		
		t1所创建而分配的数组, 将同时分配给t1, t2, t3, 被删除3次。
		所导致结果是无意义的


	>* 清楚定义复制构造函数
		================ source code =======================
		Table::Table(const Table& t) {
			p = new Name[sz = t.sz];
			for (int i = 0; i < sz; i++) {
				p[i] = t.p[i];
			}
		}
		
		Table& Table::operator=(const Table& t) {
			if (this != &t) {         // 防止自复制
				p = new Name[sz = t.sz];
				for (int i = 0; i < sz; i++) {p[i] = t.p[i];}
			}
			return *this;
		}
		void f() {
			Table t1(2);
			Table t2 = t1;
			Table t3(t1);
		}
		----------- ./a.out --------------------
		in Table() sz = 2
		in ~Table() sz = 2
		in ~Table() sz = 2
		in ~Table() sz = 2
		===================================================
	
	>* 原因
		在复制构造函数中, 对之前未初始化的储存区域初始化, 而不会造成储存丢失的情况。这在C++中用new和delete我不清楚在C和其他语言中如何做到, delete有何劣势等等。
		
		而复制赋值符要对结构本身有些优化, 如： 防止自赋值, 删除那些老元素, 初始化, 复制那些新元素, 通常每个非静态成员都必须复制, 可以通过异常去报告复制中的出错。
----------------------------------------------------------------------------------------
		
3, 类和数组的关系
	>* 发现问题
		为什么能对指针类型使用索引?
	
	>* 测试
		========= source code =====================
		class Name {
			const char* s;
		};
	
		void test_array() {
			Name* t;
			t[0];          // t是指针也是数组
			
			int i = 5;
			int* ptr_i = &i;
			int r = ptr_i[0];     // ptr_i是指针也是数组
			std::cout << "r = " << r << std::endl;    // prints r = 5
		}
		=========================================
		
	>* 某一类型的指针, 也同样是指向某一类型的数组。
		到现在为止, 可以把指针理解成一个表。 比如 int i, j, k; 三个整型。如何用一个变量将他们表示? 
		========= source code ===========
		int a = 2, b = 3, c = 4;
		int table_v[] = {a, b, c};
		std::cout << *table_v << std::endl;
		-------./a.out---------------------
		2
		=================================
		
		
```
