---
title: 「AGC045E」Fragile Balls
categories: 题解
urlname: AGC045E
date: 2020-08-28 23:19:23
tags:
- AtCoder
- 图论
- 构造
top:
---

## 题意简述

有 $n$ 个箱子 $m$ 个球，第 $i$ 个球开始在箱子 $a_i$，你希望把它移到箱子 $b_i$。
但是移动有限制：想要把某个球移动到某个箱子中，该球原先所在的箱子中**至少要有两个球**；并且，每个球都是易碎的，所以第 $i$ 个球不能移动超过 $c_i$ 次。

请问是否能达成目标，并求出至少要移动多少次球。保证目标状态中任意箱子中都至少有一个球。$1\le n, m, c_i\le 10^5$。

<!-- more -->

## 主要思路

首先对于 $a_i \neq b_i$ 的球，移动至少 1 次是不可少的，故先给答案加上这样的球的个数。

不妨把箱子看作点，球 $i$ 看作边 $a_i\rightarrow b_i$。显然，可以在钦定边无向的情况下先把连通块全拆出来。注意到每个点都有入度，故不可能出现树的连通块。
然后就有了三种连通块：
0. 只有一个点的自环。
1. 单个环。
2. 其他连通块。

第 0 类连通块不用操作次数，而第 2 种可以自己构造出每个球只用移动 1 次的合法方案。
于是如果没有第 1 类连通块，此时已经做完了。

发现从第 1 类连通块外移动一个球到块内，之后块内每个球也只用移动 1 次。不妨称此过程为「处理」。
设第 1 类连通块有 $x$ 个。显然将一个块外球移动到块内这 1 步是不可少的，所以也可以先扔到答案里面。

考虑将第 $i$ 个球移出其所在连通块来**处理其他连通块**：
- 若其在第 2 类连通块内，且 $a_i \neq b_i$，则其最多可处理 $c_i - 1$ 个连通块，**不需要额外操作次数**。
- 若其在（已处理的）第 1 类连通块内，则其最多可以处理 $c_i - 1$ 个连通块，而处理该连通块的操作次数在上文已经计算，故同样**不需要额外操作次数**。
- 若其在第 2 类连通块内，且 $a_i = b_i$，则其最多可处理 $c_i - 1$ 个连通块，且由于其**本来不必移动**，故**需要额外操作次数 $1$ 次**。
- 若其在第 0 类连通块内，则其最多可处理 $c_i - 2$ 个连通块，且由于其本来不必移动，并且其**移动前需要另一球来「处理」**，故**需要额外操作次数 $2$ 次**。

如何保证 $i$ 球能够移动（即能够安排先后顺序使得其移动前所在连通块已经被处理）？只需先取一个第 2 连通块中的球开始移动。

然后显然不需要额外操作次数的都随便拿。于是只剩下额外操作次数为 $1, 2$ 的球，然后排个序 Two Pointers 即可。

除排序外，复杂度 $O(n)$。

## 参考代码

{% fold %}
```cpp
#include <bits/stdc++.h>
namespace my_std {
using namespace std;
#define reg register
#define Rint register int
#define FOR(i, a, b) for (register int i = (a), ed_##i = (b); i <= ed_##i; ++i)
#define ROF(i, a, b) for (register int i = (a), ed_##i = (b); i >= ed_##i; --i)
typedef long long i64;
#define Templ(T) template <typename T>
inline int read() {
    reg int ans = 0, f = 1;
    reg char c = getchar();
    while (!isdigit(c)) f ^= (c == '-'), c = getchar();
    for (; isdigit(c); c = getchar()) ans = (ans << 1) + (ans << 3) + (c ^ 48);
    return f ? ans : -ans;
}
Templ(_Tp) inline int chkmin(_Tp &x, _Tp y) { return x > y ? x = y, 1 : 0; }
Templ(_Tp) inline int chkmax(_Tp &x, _Tp y) { return x < y ? x = y, 1 : 0; }
} // namespace my_std
using namespace my_std;

#define N 100010
#define pb push_back
int n, m, a[N], b[N], c[N];
int oud[N], f[N], s[N], r[N];
//out degree, father, size, ring
vector<int> q[4];
i64 ans;
inline int find(int x){ return x == f[x] ? x : (f[x] = find(f[x])); }
//0类：自环自成连通块
//1类：环自成连通块
//2/3类：其他连通块（中的自环/其他边）

int main() {
    n = read(), m = read();
    FOR(i, 1, n) f[i] = i, r[i] = 1;
    FOR(i, 1, m){
        a[i] = read(), b[i] =read(), c[i] = read();
        ++oud[a[i]], ans += a[i] != b[i];
        f[find(a[i])] = find(b[i]);
    }
    FOR(i, 1, n){
        ++s[find(i)];
        if(oud[i] != 1) r[find(i)] = 0;
    }

    reg i64 cnt(0);
    FOR(i, 1, n){
        cnt += find(i) == i && s[i] > 1 && r[i];
    }
    if(!cnt) return printf("%lld\n", ans), 0;
    ans += cnt;
    //无环
    FOR(i, 1, m){
        if(c[i] == 1) continue;
        Rint u = (find(a[i]));
        if(r[u]){
            if(s[u] == 1) q[0].pb(c[i] - 2);
            else if(s[u] > 1) q[1].pb(c[i] - 1);
        }
        else{
            q[a[i] == b[i] ? 2 : 3].pb(c[i] - 1);
        }
    }
    reg i64 sum(0);
    FOR(o, 0, 3) for(Rint i: q[o]) sum += i;
    if(sum < cnt) return puts("-1"), 0;
    //可用次数不够
    if(q[2].empty() && q[3].empty()) return puts("-1"), 0;
    //没有能开始贡献的
    FOR(o, 0, 3) sort(q[o].begin(), q[o].end());
    if(q[3].size()) cnt -= q[3].back(), q[3].pop_back();
    else ++ans, cnt -= q[2].back(), q[2].pop_back();
    //开始贡献
    for(Rint i: q[1]) cnt -= i;
    for(Rint i: q[3]) cnt -= i;
    if(cnt <= 0) return printf("%lld\n", ans), 0;
    //无需额外次数
    reg i64 res(1145141919810364364ll);
    sum = 0;
    Rint p(0);
    for(Rint i: q[2]) sum += i;
    ROF(i, q[0].size(), 0){
        if(i < (int)q[0].size()) sum += q[0][i];
        while(p < (int)q[2].size() && sum - q[2][p] >= cnt)
            sum -= q[2][p++];
        if(sum >= cnt){
            chkmin(res, 2ll * ((int)q[0].size() - i) +
                ((int)q[2].size() - p));
        }
    }
    //2 Pointers求剩余两种各取几个
    printf("%lld\n", ans + res);
    return 0;
}s
```
{% endfold %}