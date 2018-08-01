---
title: 1017. A除以B（20)-PAT乙级
date: 2018-07-31 00:16:36
tags: PAT, 算法
---

## 1017. A除以B (20)
本题要求计算A/B，其中A是不超过1000位的正整数，B是1位正整数。你需要输出商数Q和余数R，
使得A = B * Q + R成立。

## 输入格式：
输入在1行中依次给出A和B，中间以1空格分隔。

## 输出格式：
在1行中依次输出Q和R，中间以1空格分隔。

## 输入样例：
123456789050987654321 7

## 输出样例：
17636684150141093474 3

## Solution:
思路:
  1, 用数组储存大整数, 模拟大整数除以1位数
  2, 更省空间和减少循环次数的做法: 用C++字符串读取, 模拟手动除法, 首位整数 / B, 如果得到不是0就输出, 余数*10+下一位; 

<span stype="color:red">
2优点在于, 
边计算边输出; 
首个索引与循环分开计算, 减少在循环内的判断; 
对边界条件的定义处理在一个if语句中;
用string可以减少指针带来的诸多问题
</span>


```cpp
#include <cstdio>
#include <cstring>
using namespace std;

char bs[1050];
int ans[1050];
int cnt = 0;

int main() {
  int b, q = 0, r = 0;
  scanf("%s %d", bs, &b);
  int len = strlen(bs);
  for (int i = 0; i < len; i++) {
    int x = r * 10 + bs[i] - '0';
    q = x / b;
    r = x % b;
    if (i == 0 && q == 0) continue;
    ans[cnt++] = q;
  }
  if (cnt == 0 && ans[0] == 0) printf("0");
  for (int i = 0; i < cnt; i++) { printf("%d", ans[i]); }
  printf(" %d\n", r);
  return 0;
}
```

修改后的代码:

```cpp
#include <iostream>
#include <string>
using namespace std;


int main() {
  string a; int b;
  cin >> a >> b;
  int len = a.length();
  int f = (a[0] - '0') / b;
  if ((f != 0 && len > 1)|| len == 1)
    cout << f;
  int temp = (a[0] - '0') % b;
  for (int i = 1; i < len; i++) {
    f = (temp * 10 + a[i] - '0') / b;
    cout << f;
    temp = (temp * 10 + a[i] - '0') % b;
  }
  cout << " " << temp << "\n";
  return 0;
}

```
