---
title: UVa 136-Ugly Numbers
date: 2018-07-29 17:50:01
tags: UVa, 优先队列
---

## Problem
丑数是指不能被2, 3, 5以外的其他素数整除的数。把丑数从小到大排列起来, 结果如下:
`
1,2,3,4,5,6,8,9,10,12,15,...
`
求第1500个丑数。

## Sample Input:

There is no input to this program.

## Sample Output:

The 1500'th ugly number is <number>.

## Solution

思路:
  1, 按照我原先的思路, 首先想到, 把除235以外的1500个素数给计算出来, 再从5开始判断是否会被其中的某个数整除, `1500*1500*1500(n^3);`
  2, 使用优先队列, 用已有的丑数, 生成新的丑数;
  已经有丑数x, 计算2x, 3x, 5x, (他们一定不能被除了235外的其他素数整除, 如果能整除, 其他素数就不是素数 )
  用优先队列dq存放已生成的丑数, 队首是最小数,
  生成新的丑数后队首出dq栈, 判断生成数是否已经在之前出现过(使用set)
  没出现过的加入ugly_set, push入dq栈队尾
  入完ugly_set栈, 检测数量是不是到了1500   // 这里存在bug1


bug1: 检测ugly_set数量是1500存在bug, 因为有的丑数还没有去生成新丑数时, 丑数数量已经足够, 但实际上, 这些还没生成的丑数中, 存在比已经生成丑数更小的丑数, 因此直接让他生成1500次, 1500次的pq队首代表第1500个丑数.
bug2: The 1500'th ugly number is -1735655424. 需要定义LL

skill1: priority_queue<int, vector<int>, cmp> pq来定义优先队列, cmp用结构体重载()来实现
skill2: set是一个自带排序的数据结构
skill3: 对于set类型的s, s.count(a), 可以判断集合s中是否存在元素a
skill4: long long写起来麻烦, 可以 `typedef long long LL;`定义简写的长整型

<span style="color:red">**
归纳:
新的丑数用旧的数生成, 把旧的丑数, 放在优先队列里, 数越小权重越高.
不是用set来计数, 而是用pq出队列的次数来计数ugly number的数量, set只是用来判断生成数是否之前出现过.
**</span>
  
```cpp
#include <cstdio>
#include <set>
#include <queue>     // priority_queue定义在头文件queue中
#include <vector>
// #include <algorithm>
using namespace std;

typedef long long LL;

struct cmp {
  bool operator() (const LL a, const LL b) const {
    return a > b;   // a>b, a的优先级比b小, 返回true
  }
};

set<LL> ugly;
// priority_queue<LL> pq;   // priority_queue<LL> pq; 这样定义数越大优先级越高
priority_queue<LL, vector<LL>, cmp > pq;
// priority_queue<LL, vector<LL>, greater<LL> > pq;   // 等价

const int multi[3] = {2, 3, 5};

int main() {
  int n = 1500;
  pq.push(1); ugly.insert(1);
  for (int i = 1; ; i++) {
    LL x = pq.top(); pq.pop();
    if (i == n) {
      printf("The 1500'th ugly number is %lld.\n", x);
      break;
    }
    for (int j = 0; j < 3; j++) {
      LL x2 = x * multi[j];
      if (!ugly.count(x2)) { pq.push(x2); ugly.insert(x2);}
    }
  }

  // 对set进行排序(set已经排序好了, 不需要重排)
  // set<int>::iterator it = ugly.end();
  // printf("The 1500'th ugly number is %d.\n", *(--it));  // 不是用set来计数
  return 0;
}

```
