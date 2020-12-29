---
title: 树与图上的动态规划III
date: 2020-12-29 11:53:44
tags: 动态规划
categories: Data Structure
mathjax: true
---

洛谷p1122 最大子树和

>题目中给出了N个点的权值，与N-1条边的连接关系，并未给出根。
>
>$1 \le N \le 16000$

开始总是想需不需要换根，该题本质上是求树上点 权值和最大的一个连通分量，因此选择哪个点为根对结果没有影响，任一连通分量在某一时刻总是可以视为一颗以某个点为根的数。
$$
f[u] += max(f[v], 0) \\
ans = max(ans, f[u])
$$

```c++
#include<iostream>
#include<cstdio>
#include<climits>

using namespace std;
int f[16005], s[16005], head[40000], tot, ans = INT_MIN;

struct edge{
	int to, nxt;
} e[40000];

void add(int u, int v) {
	e[++tot] = {v, head[u]};
	head[u] = tot;
}

void dfs(int u, int fa) {
	f[u] = s[u];
	for (int i = head[u]; i; i = e[i]. nxt) {
		int v = e[i].to;
		if (v == fa) continue;
		dfs(v, u);
		f[u] += max(f[v], 0);
	}
	ans = max(ans, f[u]);
}

int main(int argc, char *argv[]) {
	ios::sync_with_stdio(false);
	int n;
	cin >> n;
	for (int i = 1; i <= n; ++i) {
		cin >> s[i];
	}
	int u, v;
	for (int i = 0; i < n - 1; ++i) {
		cin >> u >> v;
		add(u, v);
		add(v, u);
	}
	dfs(1, 0);
	cout << ans << endl;
	return 0;
}
```

洛谷 p1613 跑路

>题目大意，图结构，如果i到j的距离为$2^k$千米，那么只需要1s时间。
>
>$n \le 50, m \le 10000$

因为涉及到$2^k$，因此为倍增算法，先进行预处理，状态表示f\[i]\[j]\[k]表示i到j有一条距离为$2^k$的边。
$$
f[i][j][k] = f[i][l][k - 1] \&\& f[l][j][k - 1]  \\
1 \le l \le n
$$

```c++
#include <bits/stdc++.h>

using namespace std;
int dis[60][60];
bool f[60][60][65];
int n, m;

void work() {
    for (int l = 1; l <= 64; ++l) {
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                for (int k = 1; k <= n; ++k) {
                    f[i][j][l] = f[i][k][l - 1] && f[k][j][l - 1];
                    if (f[i][j][l]) {
                        dis[i][j] = 1;
                        break;
                    }
                }
            }
        }
    }
}

void floyd() {
    for (int k = 1; k <= n; ++k) {
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
            }
        }
    }
}
int main()
{
    ios::sync_with_stdio(false);
    cin >> n >> m;
    memset(dis, 63, sizeof(dis));
    int u, v;
    for (int i = 0; i < m; ++i) {
        cin >> u >> v;
        dis[u][v] = 1;
        f[u][v][0] = true;
    }
    work();
    floyd();
    cout << dis[1][n] << endl;
    return 0;
}

```

