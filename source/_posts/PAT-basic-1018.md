---
title: 1018. 锤子剪刀布 (20)-PAT乙级
date: 2018-07-30 22:38:38
tags:
---

## Problem:
大家应该都会玩“锤子剪刀布”的游戏：两人同时给出手势，胜负规则如图所示：现给出两人的交锋记录，请统计双方的胜、平、负次数，并且给出双方分别出什么手势的胜算最大。

## Input:
输入第1行给出正整数N（<=105），即双方交锋的次数。随后N行，每行给出一次交锋的信息，即甲、乙双方同时给出的的手势。C代表“锤子”、J代表“剪刀”、B代表“布”，第1个字母代表甲方，第2个代表乙方，中间有1个空格。

## Output:
输出第1、2行分别给出甲、乙的胜、平、负次数，数字间以1个空格分隔。第3行给出两个字母，分别代表甲、乙获胜次数最多的手势，中间有1个空格。如果解不唯一，则输出按字母序最小的解。

## Sample Input:
10
C J
J B
C B
B B
B C
C C
C B
J B
B C
J J

## Sample output:
5 3 2
2 3 5
B B

## Solution1:
虽然考虑到了9中组合, 但写的时候思路不清晰, 甚至没有把胜出时候的手势放入数组.
冗杂在于
1, 一开始想把它放进一个struct, 但实际上只拥有六个整型, 为什么不放到数组里?
2, getMax函数纠结了好久, 实际有更为简单的方法

```cpp
struct score {
  int w = 0, f = 0, e = 0;    // 胜负平
  int C = 0, J = 0, B = 0;    // 胜的时候出的手势
};

vector<int> x;;
const char motion[4] = "BCJ";
char getMax(int a, int b, int c) {
  // a,b,c谁最大, 返回0,1,2; 并列返回字典序列
  x.clear();
  int m;
  m = max(a, b); m = max(m, c);
  if (m == a) x.push_back(0);
  if (m == b) x.push_back(1);
  if (m == c) x.push_back(2);
  sort(x.begin(), x.end());
  return motion[x[0]];
}


char a[2], b[2];

int main () {
  int T;
  score jia, yi;
  if (scanf("%d", &T) == 1 && T) {
    while (T--) {
      scanf("%s %s", a, b);
      if (a[0] == 'J') {
        if (b[0] == 'J') { jia.e++; yi.e++; }    // 平局
        else if (b[0] == 'C') { jia.f++; yi.w++; yi.C++; }  // 乙胜
        else if (b[0] == 'B') { jia.w++; yi.f++; jia.J++; }  // jia胜
      } else if (a[0] == 'C') {
        if (b[0] == 'C') { jia.e++; yi.e++; }    // 平局
        else if (b[0] == 'J') { jia.w++; yi.f++; jia.C++; }  // jia胜
        else if (b[0] == 'B') { yi.w++; jia.f++; yi.B++; }  // yi胜
      } else if (a[0] == 'B') {
        if (b[0] == 'B') { jia.e++; yi.e++; }    // 平局
        else if (b[0] == 'J') { jia.f++; yi.w++; yi.J++; }  // yi胜
        else if (b[0] == 'C') { yi.f++; jia.w++; jia.B++; }  // jia胜
      }
    }
    printf("%d %d %d\n", jia.w, jia.e, jia.f);
    printf("%d %d %d\n", yi.w, yi.e, yi.f);
    printf("%c", getMax(jia.B, jia.C, jia.J));
    printf(" %c\n", getMax(yi.B, yi.C, yi.J));
  }



  return 0;
}

```

## Solution2:
将字符转换为整数进行计算
```
00  平   10 甲   20 乙
01  乙   11 平   21 甲
02  甲   12 乙   22 平
```

可以发现, -2, 1时, 甲胜;  -1, 2时, 甲负
甲乙胜负可以转换, 平局可以通过总局数得到, 因此只需要记录甲的胜负


```cpp
#include <cstdio>
#include <iostream>
#include <map>
using namespace std;

map<char, int> s_c_i = {};    // 将字符转换成整数, 用于索引和计算
const char str[4] = "BCJ";

int main() {
  int T;
  int jia[3] = {0}, yi[3] = {0};
  if (scanf("%d", &T) == 1 && T) {
    s_c_i['J'] = 0, s_c_i['C'] = 1, s_c_i['B'] = 2;  // 将字符转换成整数用于计算
    int w = 0, f = 0, ct = T;                        // 甲胜利, 失败, 总局数
    while (T--) {
      char sa, sb;
      cin >> sa >> sb;
      int d = s_c_i[sa] - s_c_i[sb];
      if (d == -2 || d == 1) {    // 甲胜
        w++; jia[s_c_i[sa]]++;
      } else if (d == -1 || d == 2) {   // 甲负, 乙胜
        f++; yi[s_c_i[sb]]++;
      }
    }
    printf("%d %d %d\n", w, ct-w-f, f);
    printf("%d %d %d\n", f, ct-w-f, w);
    int jiamax = (jia[0] >= jia[1]) ? 0 : 1;
    jiamax = (jia[jiamax] >= jia[2]) ? jiamax : 2;
    int yimax = (yi[0] >= yi[1]) ? 0 : 1;
    yimax = (yi[yimax] >= yi[2]) ? yimax : 2;
    printf("%c", str[jiamax]);
    printf(" %c\n", str[yimax]);
  }
  return 0;
}
```


## Solution 3:

<span style="color:red">
只记录甲的胜负, 计算出乙胜负平; 六种排列组合; 三个计数器放在数组中, 用 ?:判断哪个位置计数最高
能用数组解决的不用struct的, 能用原子类型解决的不用数组;
</span>

```cpp
#include <iostream>
#include <cstdio>
using namespace std;

const char str[4] = {'B', 'C', 'J'};

int main () {
  int T;
  int win = 0, fail = 0;   // 只记录甲的胜负
  int winjia[3] = {0}, winyi[3] = {0};
  cin >> T;
  int ct = T;
  while (T--) {
    char a, b;
    cin >> a >> b;
    // 六种排列组合
    if (a == 'B' && b == 'C') {
      win++; winjia[0]++;
    } else if (a == 'B' && b == 'J') {
      fail++; winyi[2]++;
    } else if (a == 'C' && b == 'B') {
      fail++; winyi[0]++;
    } else if (a == 'C' && b == 'J') {
      win++; winjia[1]++;
    } else if (a == 'J' && b == 'B') {
      win++; winjia[2]++;
    } else if (a == 'J' && b == 'C') {
      fail++; winyi[1]++;
    }
  }
  printf("%d %d %d\n", win, ct-win-fail, fail);
  printf("%d %d %d\n", fail, ct-win-fail, win);
  int jiamax = (winjia[0] >= winjia[1]) ? 0 : 1;
  jiamax = (winjia[jiamax] >= winjia[2]) ? jiamax : 2;
  int yimax = (winyi[0] >= winyi[1]) ? 0 : 1;
  yimax = (winyi[yimax] >= winyi[2]) ? yimax : 2;
  printf("%c", str[jiamax]);
  printf(" %c\n", str[yimax]);

  return 0;
}

```
