---
title: C++ Zero To One 0.006
date: 2018-01-16 22:30:14
tags:
---

```
1, 局部变量的销毁顺序
	=============================
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
	================================

2, 构造函数复制
```
