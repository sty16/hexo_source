---
title: leetcode 418 屏幕可显示句子的数量
date: 2020-12-20 20:46:39
tags: Data Structure
categories: Data Structure
mathjax: true
---

### 题目描述

>给你一个 `rows x cols` 的屏幕和一个用 **非空** 的单词列表组成的句子，请你计算出给定句子可以在屏幕上完整显示的次数。
>
>+ 单词不能拆分成两行。
>+ 单词在句子中的顺序必须保持不变。
>+ 在一行中 的两个连续单词必须用一个空格符分隔。
>+ 句子中的单词总量不会超过 100。
>+ 每个单词的长度大于 0 且不会超过 10。
>+ 1 ≤ rows, cols ≤ 20,000.

#### 方法1 暴力模拟(超时）

​		开始看这道题没什么思路，放置单词这个应该没有什么规律，只能去暴力计算放置字符串的位置，模拟了一下在pos这个位置放置一个句子，下一次放置该句子的位置与行数。无奈被这个数据给卡了["a"], 20000, 20000，按句子模拟必然超时。

### 方法2 动态规划

​		这道题的trick在于单词数量比较少，不超过100个，并且当单词不能放入当前行时，下一次其一定放置在下一行的行首。这样可以定义两个状态，这样预处理的时间复杂度为$O(mN)$, 计算放置句子个数的过程时间复杂度为$O(N)$
$$
count[j] \quad 以第j个单词为当前行第一个单词时，放置的句子的个数 \\
next[j] \quad 以第j个单词为当前行第一个单词时，下一行第一个单词
$$

```c++
class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        int n = sentence.size();
        vector<int> cnt(n, 0), next(n, 0);
        vector<int> arr;
        for (string s : sentence) {
            arr.push_back(s.size());
        }
        for (int i = 0; i < n; ++i) {
            int tmp = 0, idx = i;
            for (int j = 0; j <= cols + 1;) {
                j += arr[idx] + 1;
                if (j > cols + 1) break;
                if (idx == n - 1) tmp++;
                idx = (idx + 1) % n;
            }
            cnt[i] = tmp;
            next[i] = idx;
        }
        int ans = 0, idx = 0;
        for (int i = 0; i < rows; ++i) {
            ans += cnt[idx];
            idx = next[idx];
        }
        return ans;
    }
```

