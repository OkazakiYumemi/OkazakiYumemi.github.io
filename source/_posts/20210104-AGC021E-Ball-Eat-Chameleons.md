---
title: 「AGC021E」球 Eat Chameleons
categories: 题解
urlname: AGC021E
date: 2021-01-04 16:38:43
tags:
- AtCoder
- 计数
top:
---

## 题意简述

[AGC 021E](https://atcoder.jp/contests/agc021/tasks/agc021_e)

有 $n$ 条蛇，开始都是蓝色。你有 $k$ 个球，均为红色或蓝色，可以依次喂给蛇。
一条蛇如果吃了一个球之后，吃的该颜色的球的总数恰好超过另一种颜色的球，则该蛇会变为该颜色。
一个球的颜色序列合法仅当可以合理分配喂蛇顺序使得最后所有蛇都能变为红色。
$n, k\le 5\times 10^5$。
~~（由于「🐍️」的渲染效果无法预测，故用「蛇」。）~~

<!-- more -->

## 主要思路

下面用`B`来表示蓝色球，`R`来表示红色球。

一个显然的事实是，如果给两条未喂过球的蛇都喂了一些`B`，显然没有将这些`B`都喂给其中一条优。
考虑将这些蓝色的蛇喂回红色需要的`R`数量即可得到。
那么，一个球的颜色序列**最后能够得到的红蛇最多的方案**应该为：有一条蛇（下称「大蛇」）吃了最后一个球，并且为唯一一条可能吃了超过 $2$ 个球的蛇；其他蛇的吃球序列仅可能为`RB`或`R`。
（一个球的颜色序列**最后能够得到的红蛇最多的方案**即，开始假设有无数条蓝蛇，将这个球的颜色序列按顺序喂给蛇后使得最后所有喂过的蛇均为红色，并且红色的蛇的数量最多的一个方案。）

另一个显然的事实是，如果一个球的颜色序列**最后能够得到的最多红蛇数量**为 $m\ge n$，则其一定能够得到 $n$ 条红蛇。
于是，我们考虑**最后能够得到的最多红蛇数量**为 $m$ 的球的颜色序列。

除了大蛇以外应该还有 $m-1$ 条蛇，注意到这些蛇都会且仅会吃 $1$ 个`R`，不妨枚举这些`R`的位置；并且注意到上述定义中吃了最后一个球的是大蛇（实质上是为了钦定大蛇的位置，否则类似`RBRBRB`的序列则不知哪个是大蛇），所以这些`R`的位置仅能是前 $k-1$ 个球。
于是这里有 $\binom{k-1}{m-1}$ 种**放置其他蛇的`R`**的方案。

根据分析可得，每一种**放置其他蛇的`R`**的方案都与**最后能够得到的最多红蛇数量**为 $m$ 的球的颜色序列一一对应。
于是答案即为 $\sum\limits_{m=n}^{k}\binom{k-1}{m-1}$。
<br>

~~等等，证明呢~~
**以下内容不保证严谨性。**
证明一个**放置其他蛇的`R`**的方案能够推出唯一一个**最后能够得到的最多红蛇数量**为 $m$ 的球的颜色序列：
考虑现在已经放置了 $m-1$ 个其他蛇的`R`，现在我们做以下操作：
从后到前依次考虑每个`R`的位置，在下一个空位放上一个`B`，构成一条吃球顺序为`RB`的蛇。
若一个`R`没有下一个空位，表示该蛇的吃球顺序为`R`。

考虑剩下的位置（包括最后一个位置），假设有 $c$ 个。
如果 $c$ 为奇数，则依次在前 $c-1\over 2$ 个空位填上`B`，其他填上`R`；
否则依次在前 ${c\over 2}-1$ 个位置与最后一个位置填上`B`，其他空位填上`R`。

此时即可满足，得到的颜色序列**最后能够得到的红蛇数量**为 $m$ ，且**其他蛇的`R`尽量后**的方案中，其他蛇的`R`是开始选出的`R`。

以下是一个例子，用于感受上述过程：
```plain
k = 12, m = 5
..R.R...RR.
..RBRB..RRB
BBRBRBRRRRBR
```
<br>

证明一个**最后能够得到的最多红蛇数量**为 $m$ 的球的颜色序列能够推回唯一一个**放置其他蛇的`R`**的方案：

注意到在上述为每个其他蛇的`R`放置`B`后，整个序列中已经放置球的每个极长段，都有其中的`R`均与其中的`B`匹配（这里认为无`B`与其匹配的`R`不在极长段内）。
并且，由于大蛇的吃球序列的特殊性（`B...BR...R`或`B...BR...RB`），放置大蛇吃掉的球后，前 $k-1$ 个球中不会有更长的极长段产生。

所以同理地，找出这些极长段（可以使用类似括号匹配的方式），剩余的序列即为大蛇的吃球序列和一些未匹配的`R`。
显然这些未匹配的`R`只能是这个剩余的序列**次长前缀的一个后缀**，找出最长的这样的后缀即可；剩余的即为大蛇的吃球序列。
如果这个剩余的序列本身都无法使大蛇变为红色，则该序列本身不合法。

以上是一个例子，用于感受上述过程：
```plain
k = 14
)(()()())((()(
BRRBRBRBBRRRBR
B........RR..R +5
B........R...R +1
m = 6
```

综上，即可得每一种**放置其他蛇的`R`**的方案都与**最后能够得到的最多红蛇数量**为 $m$ 的球的颜色序列一一对应。

## 参考代码

{% fold %}
```cpp
#include <bits/stdc++.h>
using namespace std;
#define FOR(i, a, b) for (int i = (a), ed_##i = (b); i <= ed_##i; ++i)
#define ROF(i, a, b) for (int i = (a), ed_##i = (b); i >= ed_##i; --i)
typedef long long i64;
#define mod 998244353
inline int ksm(int x, i64 y) {
    int res = 1;
    for (; y; y >>= 1, x = (i64)x * x % mod)
        if (y & 1) res = (i64)res * x % mod;
    return res;
}
inline void inc(int &x, const int &y){ x += y; if(x >= mod) x -= mod; }

#define N 500010
int n, K;

int fac[N], ifac[N];
void init(int T){
    *fac = *ifac = 1;
    FOR(i, 1, T) fac[i] = (i64)i * fac[i - 1] % mod;
    ifac[T] = ksm(fac[T], mod - 2);
    ROF(i, T - 1, 1) ifac[i] = (i + 1ll) * ifac[i + 1] % mod;
}
inline int CC(int n, int m){
    if(m < 0 || n < m) return 0;
    return (i64)fac[n] * ifac[m] % mod * ifac[n - m] % mod;
}

int main() {
    scanf("%d%d", &n, &K);
    init(K);
    int ans = 0;
    FOR(i, n, K) inc(ans, CC(K - 1, i - 1));
    printf("%d\n", ans);
    return 0;
}
```
{% endfold %}