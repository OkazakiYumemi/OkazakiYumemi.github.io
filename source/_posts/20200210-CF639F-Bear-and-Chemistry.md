---
title: 「CF639F」Bear and Chemistry
date: 2020-02-10 23:35:57
tags:
- 图论
- Tarjan
- 虚树
- Codeforces
categories: 题解
top:
urlname: CF639F
---

## 题意简述

[[CF 639F]](https://codeforces.com/problemset/problem/639/F)

给定一张无向图 $G = (V, E)$ 其中点数 $n$ ，边数 $m$ 。$q$ 次询问，第 $i$ 次询问，会把无向图加上一些新的边，并询问一个大小为 $n_i$ 的点集在加入了给定 $m_i$ 条边的情况下，是否对于这个点集中的每一对点对 $(x, y)$ 都存在一条从 $x$ 到 $y$ 再回到 $x$ 的不经过重复边的路径。询问独立且强制在线。

$1\le n, q\le 3\times 10^5, 0\le m\le 3\times 10^5, \sum{n_i}, \sum{m_i} \le 3\times 10^5$ 。

<!-- more -->

## 主要思路

显然，问题可以转化为询问加入一些边的情况下，某个点集是否在同一边双内。

可以先将原先给的图缩成边双，然后建成边双树。对于每次询问，将加边所在的点和询问的点集作为关键点。对关键点建虚树，再对虚树的边和新加的边求边双，判断询问点集是否在同一边双即可。

时间复杂度 $O(n\log n)$ （所有数与 $n$ 同阶）。

## 参考代码

咕咕咕