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

请问是否能达成目标，并求出至少要移动多少次球。$n, m, c_i\le 10^5$。

<!-- more -->

## 主要思路

咕

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