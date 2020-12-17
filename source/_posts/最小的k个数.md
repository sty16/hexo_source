---
title: 最小的k个数
date: 2020-12-17 23:00:13
tags: Data Structure
categories: Data Structure
mathjax: true
---

[leetcode 373 查找和最小的K对数字](https://leetcode-cn.com/problems/find-k-pairs-with-smallest-sums/)

题目描述：给定两个以升序排列的整形数组 nums1 和 nums2, 以及一个整数 k。定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2。找到和最小的 k 对数字 (u1,v1), (u2,v2) ... (uk,vk)。

#### 做题思路

由于题目中没有给定数据范围，开始以为是双指针问题，采用贪心策略，但是举了两个例子，指针的移动无法采用贪心的方法，如[1, 3, 100]与[2, 10, 11]， 第二组为(2, 3)，第三组为(1, 10)，第一个数组的指针出现了回退，因此贪心不可行。

##### 1.暴力做法

​		枚举数组1与数组2所有可能的组合，并采用[箭指offer 40 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)的最大堆的方法，维护最小的k对数字，时间复杂度$O(n^2logk)$，运行时间1232ms，勉强过了。

##### 2.利用数组的排序信息

​		在暴力做法中完全没有利用到题目中给出的数组为升序排列这个信息，还是以[1, 3, 100]与[2, 10, 11]为例，开始我们将$(nums1[0], nums2[j]) j = 1,2\dots n$放入最小堆中，如果当前堆中弹出的为$(nums1[i], nums2[j])$，那么则将$(nums1[i + 1], nums2[j])$放入到最小堆中。

数据结构`pair<pair<int, int>, int>`，分别记录nums1的值、下标， nums2的值。

最小堆 `priority_queue<pair<pair<int, int>, int>, vector<pair<pair<int, int>, int>>, cmp>`

```c++
struct cmp {
    bool operator ()(const pair<pair<int, int>, int>&a, const pair<pair<int, int>, int>& b) const {
        return a.first.first + a.second > b.first.first + b.second;
    }
};

class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        int m = nums1.size(), n = nums2.size();
        if (m == 0 || n == 0) return {};
        priority_queue<pair<pair<int, int>, int>, vector<pair<pair<int, int>, int>>, cmp> pq;
        for (int i = 0; i < n; ++i) {
            pq.push({{nums1[0], 0}, nums2[i]});
        }
        int cnt = 0;
        vector<vector<int>> ans;
        while (!pq.empty() && cnt < k) {
            auto [a, b] = pq.top();
            pq.pop();
            ans.push_back({a.first, b});
            int idx = a.second;
            if (idx + 1 < m) pq.push({{nums1[idx + 1], idx + 1}, b});
            ++cnt;
        }
        return ans;
    }
};
```



