---
title: 树与图上的动态规划II
date: 2020-12-28 20:37:20
tags: Data Structure
categories: Data Structure
mathjax: true
---

洛谷P2014 选课

>在大学里每个学生，为了达到一定的学分，必须从很多课程里选择一些课程来学习，在课程里有些课程必须在某些课程之前学习，如高等数学总是在其它课程之前学习。现在有 NNN 门功课，每门课有个学分，每门课有一门或没有直接先修课（若课程 a 是课程 b 的先修课即只有学完了课程 a，才能学习课程 b）。一个学生要从这些课程里选择 MMM 门课程学习，问他能获得的最大学分是多少？

每门课只有一门先修课， 因此该图结构为森林，假设没有选修课的父节点都指向零，因此如果要选择子节点，则必须要选择父节点，因为父节点为选修课的要求。

转换为树上背包问题
$$
dp[i][j] = max(dp[i][j], dp[i][j - k] + dp[x][k]) \quad x为i的子节点。
$$

```c++
#include<iostream>
#include<cstdio>
#include<cstring>

using namespace std;
int f[305][305], head[305], tot, scores[305], sz[305];

struct edge{
	int to, nxt;
} edges[305];

void add(int u, int v) {
	edges[++tot] = {v, head[u]};
	head[u] = tot;
}

void dfs(int u) {
	sz[u] = 1;
	f[u][1] = scores[u];
	for (int i = head[u]; i; i = edges[i].nxt) {
		int v = edges[i].to;
		dfs(v);
		sz[u] += sz[v];
		for (int j = sz[u]; j >= 1; --j) {
			for (int k = min(j - 1, sz[v]); k >= 0; --k) {
				f[u][j] = max(f[u][j], f[u][j - k] + f[v][k]);
			}
		}
	}
}

int main(int argc, char *argv[]) {
	int n, m;
	cin >> n >> m;
	int k, s;
	for (int i = 1; i <= n; ++i) {
		cin >> k >> scores[i];
		add(k, i);
	}
	dfs(0);
	cout << f[0][++m] << endl;  //注意包含了0节点，因此要多选一门
	return 0;
} 
```

