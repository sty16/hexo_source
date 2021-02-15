---
title: 线段树I
date: 2021-02-05 18:03:40
tags: Data Structure
Categories: Alogrithm
mathjax: true
---

### 线段树

线段树本质上开了O(4n)的空间来维护了数组区间的性质。根节点从下标1开始

```c++
void build(int p, int l, int r) {
    // p为当前l, r区间对应的下标， l为当前区键的左侧下标，r为右侧下标
    if (l == r) {
        //更新a[p]
        return;
    }
    int mid = l + (r - l) / 2;
    build(p << 1, l , mid);
    build(p << 1 | 1, mid + 1, r);
    a[p] = f(a[p << 1], a[p << 1 | 1])
}
```

惰性更新，当更新区间包含了当前的区间[l, r]，只需要更新当前的节点并进行标记。

```c++
void update(int p, int l, int r, int ul, int ur, int val) {
    
}
```

