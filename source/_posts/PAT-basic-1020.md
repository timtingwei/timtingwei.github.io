---
title: 1020. 月饼 (25)-PAT乙级
date: 2018-08-02 01:01:33
tags: [PAT, 算法, 贪心, 优先队列]
---

## 1020 月饼 (25)

月饼是中国人在中秋佳节时吃的一种传统食品，不同地区有许多不同风味的月饼。现给定所有种类月饼的库存量、总售价、以及市场的最大需求量，请你计算可以获得的最大收益是多少。

注意：销售时允许取出一部分库存。样例给出的情形是这样的：假如我们有3种月饼，其库存量分别为18、15、10万吨，总售价分别为75、72、45亿元。如果市场的最大需求量只有20万吨，那么我们最大收益策略应该是卖出全部15万吨第2种月饼、以及5万吨第3种月饼，获得 72 + 45/2 = 94.5（亿元）。

## 输入格式：

每个输入包含1个测试用例。每个测试用例先给出一个不超过1000的正整数N表示月饼的种类数、以及不超过500（以万吨为单位）的正整数D表示市场最大需求量。随后一行给出N个正数表示每种月饼的库存量（以万吨为单位）；最后一行给出N个正数表示每种月饼的总售价（以亿元为单位）。数字间以空格分隔。

## 输出格式：

对每组测试用例，在一行中输出最大收益，以亿元为单位并精确到小数点后2位。

## 输入样例：

3 20
18 15 10
75 72 45
输出样例：

94.50

## Solution:
计算各种月饼每吨的售价, 优先每吨售价最高的月饼填满需求, 剩下的用售价次高的填满
采用优先队列, dq放每个月饼结点, 价格越高结点会被排在更前面

<span style="color:red">注意一下测试点3(case 3)通不过的情况, 如果都供应完了, 还没达到市场需求的情况, 按当前供应量来计算价格</span>

```cpp
#include <iostream>
#include <cstdio>
#include <queue>
#include <vector>
#include <algorithm>
using namespace std;

struct Cake {
  double num, tot;
  double each_d;
};

struct cmp {
  bool operator () (const Cake c1, const Cake c2) const {
    return c1.each_d <= c2.each_d;     // 每吨的价格越高权重越高
  }
};
vector<Cake> cakes;
priority_queue<Cake, vector<Cake>, cmp > dq;


int main() {
  int N, D;   // 种类数和市场需求
  cin >> N >> D;
  for (int i = 0; i < N; i++) {
    Cake tmp_c;
    cin >> tmp_c.num;
    cakes.push_back(tmp_c);
  }
  for (int i = 0; i < N; i++) {
    cin >> cakes[i].tot;
    cakes[i].each_d = cakes[i].tot / cakes[i].num;
    dq.push(cakes[i]);
  }
  double ans = 0;
  int now_d = 0;
  for (;;) {
    // debug case3: 如果都供应完了, 还没达到市场需求的情况
    if (dq.empty()) break;
    Cake cmax = dq.top();
    if (D - now_d >= cmax.num) {   // 如果需求量比当前最有优价格的供货量大
      now_d += cmax.num;
      ans += cmax.each_d * cmax.num;
    } else {    // 只需要提供一部分
      ans += (D - now_d) * cmax.each_d;
      now_d = D;    // 供应部分后, 满足市场需求
      break;
    }
    dq.pop();   // 队首月饼供应完毕
  }
  printf("%.2lf\n", ans);
  return 0;
}
```

## Solution2:

第二遍做, 优先队列的逻辑更直观, 注意正数和正整数的区别, 要将cnt声明成double, 不然case2过不了

```cpp
#include <cstdio>
#include <queue>
#include <vector>
using namespace std;

struct food {
  double scale, each_scale;
  double cnt;
};

struct cmp {
  bool operator() (const food &f1, const food &f2) {
    return f1.each_scale < f2.each_scale;
  }
};

bool db_equal(const double d1, const double d2) {
  return -0.0005 < d1-d2 && d1-d2 < 0.00005;
}

int main() {
  int N;
  double need; scanf("%d %lf", &N, &need);
  vector<food> v(N);
  priority_queue<food, vector<food>, cmp> q;
  for (int i = 0; i < N; i++) {
    scanf("%lf", &v[i].cnt);
  }
  for (int i = 0; i < N; i++) {
    scanf("%lf", &v[i].scale);
    v[i].each_scale = v[i].scale / v[i].cnt;
    q.push(v[i]);
  }
  double profit = 0.0;
  while (!db_equal(need, 0.0) && !q.empty()) {
    if (q.top().cnt > need) {
      profit += q.top().each_scale * need;
      need = 0;
    } else {  // <= need
      profit += q.top().scale;
      need -= q.top().cnt;
      q.pop();
    }
    if (q.empty()) break;
  }
  printf("%.2lf", profit);
  return 0;
}
```
