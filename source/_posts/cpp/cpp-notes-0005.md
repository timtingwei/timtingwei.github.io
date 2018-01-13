---
title: C++ Zero To One 0.005
date: 2018-01-13 21:42:33
tags: [C++, const, 常量指针, 常量引用, protected, for]
---
```
1, protected能否被实例对象访问

	>* 发现问题	
		重新实现Vector中, 要对ADT的access进行区别。
		protected能否被实例对象访问?
		protected能否被继承类访问?
	>* 尝试
		=================== source code ===========================
		class Base {
		private:
			int i = 0;
			void f_priv() {std::cout << "calling f_priv() in private" << std::endl;}
		protected:
			void f_prot() {std::cout << "calling f_prot() in protected" << std::endl;}
		public:
			void f() {
				std::cout << "calling f() in public" << std::endl;
				f_prot();
			}
		};
		
		class Derived : Base {
		public:
			void call_f_prot() {f_prot();}
			// void call_f_priv() {f_priv();}    // private不能被继承类直接访问
		};
		
		int main() {
			Base b;
			Derived d;
			// b.f_prot();      // protected成员不能被实例对象直接访问
			d.call_f_prot();    // 通过继承类的public成员, 访问基类的protected
			// d.call_f_priv();
			return 0;
		}
		=============================================================
		
	>* 特点
		protected不会被实例访问, 会被继承类在类中进行访问。
		
		希望用户不能访问到protected中的成员, 但继承类能够访问到。
		而private让用户和继承类都不能访问到, 只有友元能访问到。
---------------------------------------------------------------------------------------------

2, for循环语句的简单写法
	
	>* 发现问题
	for语句的三个表达式如何写?
	Vector(int c = DEAFAULT_CAPACITY, int s = 0, T v = 0)
	{_elem = new T[_capacity = c];
		for (_size = 0; _size < s; _elem[size++] = v)}     // 默认
		
	>* 利用for的参数
		for ( declaration-or-expression(optional) ; 
			declaration-or-expression(optional) ; 
			expression(optional) )
		=================== source code ============================
		const int s = 8;
		int size = 0;
		int elem[s] = {};
		/*
		for (size = 0; size < s; size++) {
			elem[size] = 2;
		}
		*/
		// 第三个参数, 递增size的同时, 给修改elem
		for (size = 0; size < s; elem[size++] = 2) {}
		// 第三个参数, 修改i的同时, 打印elem[i]对应的元素
		for (int i = 0; i < size; std::cout << elem[i++] << std::endl) {}
		============================================================
	>* 反思
		某变量会让循环条件false, 且该变量又是语句中的某一要素时候，一举两得。
----------------------------------------------------------------------------------------------

3, T const&, const T&, T& const, T const*, const T*, T* const 辨析
	
	>* 发现问题
		=================== source code ============================
		Vector(T* const A, Rank lo, Rank hi)           // 数组区间复制
		Vector(Vector<T> const& V, Rank lo, Rank hi)   // 向量区间复制
		============================================================
		对上述代码中的 Vector<T> const& V不理解, 为什么const& 跟在type之后? 它是否表达常量引用?

	>* 常量引用
		type const&, type& const之间是什么区别 ?
		先回答: 没区别。 但偏向写成前者, 能直接看出这是一个引用类型, 且为常量引用.
		
		让测试代码跑跑。
		=================== source code =============================
		int f() {
		int a = 0, b = 1;
		const int ca = 0, cb = 1;
		
		int& ref_a = a;      // ref_a是整数a的引用类型
		int* ptr_a = &a;     // ptr_a是指向整数a的指针
		const int* ptr_ca = &ca;           // ptr_ca是指向常量整型的指针
		const int* const cptr_ca = &ca;    // cptr_ca是指向常量整型的常量指针
		int const& cref_a = a;             // cref_a是对整型的常量引用?
		const int& cref_a_1 = ca;          // cref_a_1是对整型常量引用?
		// const int& const cref_a_2 = ca;    // 对整型常量的常量引用?
		// const int const& cref_a_3 = ca;    // 还是这样? 对整型常量的常量引用?
		// 引用不能duplicate const
		f1(cref_a);       // 将cref_a传入f1(), cref_a类型为 int const&匹配
		// cref_a++;      // read-only    // 两者都是常量引用
		// cref_a_1++;    // read-only
		
		// 引用指针
		int* const& cref_ptra = ptr_a;            // 对整型指针的常量引用
		// int const& cref_ptra_1 = ptr_a;        // 错误: 引用对象类型int*
		const int* const& cref_ptrca = ptr_ca;    // 对指向常量指针的常量引用
		// int* const& cref_ptrca_1 = ptr_ca;     // 错误: 引用对象类型const int*
		const int* const& cref_cptrca = cptr_ca;
		
		(*cref_ptra)+=1;          // cref_ptra整型指针的常量引用, 改变指向整型
		// (*cref_cptrca)+=1;     // ERROR: 无法通过引用, 改变指向常量的常量指针指向的整数(常量)
		}
		
		void f1(int const& ic) {
			std::cout << "calling f1()...\n";
		}
		=============================================================
		
		常量引用的写法. 所引用的对象类型为type
		(type) const& ref_t;
		// const&写在右边是因为侧重于表明其常量指针
		
		另外, 这一type可以是int, const int, int*, const int*等等
		
	>* 常量指针
		===================== source code ===========================
		int a = 0, b = 0, c = 0, d = 0;
		int* p_a = &a;                 // 指向int的指针
		// 指针地址可变, 指向的a可变
		(*p_a)++; p_a = &b;
		std::cout << "*p_a = " << *p_a << ", a = " << a << std::endl;
		const int* p_b = &b;          // 指向const int的指针
		// 指针地址可变, 指向的元素转换成const不可变
		// (*p_b)++;
		p_b = &c;
		std::cout << "*p_b = " << *p_b << ", b = " << b << std::endl;
		int* const cp_c = &c;          // 指向int的const指针
		// 指针地址不可变, 指向的元素可以变
		(*cp_c)++;
		// cp_c = &d;
		std::cout << "*cp_c = " << *cp_c << ", c = " << c << std::endl;
		// int const* cp_c1 = &c;         // 指向const int的指针, 仍采用上种方法
		const int* const cp_cd = &d;   // 指向const int的const指针
		// 指针地址不可变, 指向值不可变
		// (*cp_cd)++;
		// cp_cd = &a;
		std::cout << "*cp_cd = " << *cp_cd << ", d = " << d << std::endl;
		------------------------ ./a.out ---------------------------
		*p_a = 0, a = 1
		*p_b = 0, b = 0
		*cp_c = 1, c = 1
		*cp_cd = 0, d = 0
		============================================================
		T*               = &a;      // 指针
		const T*         = &ca;     // 指向常量的指针
		T* const         = &a;      // 常量指针, 指针地址不可变
		const T* const   = &ca;     // 指向常量的常量指针, 指向值和指针地址都不可变
		
	>* const_cast<type>();
		=======
		int b = 0;
		const int* p_b= &b;
		*(const_cast<int*>(p_b))+=1;   // 这种方式可以让b递增1
		// (*p_b)++                    // const_cast只是一个临时操作
		==========
		一方面可以去掉const的性质, 另外一方面又是临时的, 可以确保安全。
		我想这可能会用在, 绝大多数是const, 只有极少情况才会变的数据类型中。
	
	>*  最初的辨析
		T const& : 引用, T类型的常量引用, 该引用不会被修改
		const T& : 同上, 只是不同写法。现在偏向用上面那种
		T& const : 非法 
		T const* : 指针, 指向常量T的指针, const T*相同 
		const T* : 指针, 指向常量T的指针, 我偏向用这种
		T* const : 指针, 指向T的常量指针
---------------------------------------------------------------------------------------

```
