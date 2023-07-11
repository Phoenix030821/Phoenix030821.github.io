---
title: leetcode做题笔记
date: 2023-07-11 19:43:13
categories:
- 学习
tags:
- 学习
- 算法
mathjax: true
---

**41. 缺失的第一个正数**

题意：给一个未排序的整数数组，找其中没出现过的最小正整数。

要求时间复杂度$O(n)$，空间复杂度$O(1)$。

做出来了，但看了标签。

如果没有空间复杂度要求，直接开个长度为$n$数组，打标记即可。

有了空间复杂度要求，打标记较难，因为可以修改原数组，考虑原地打。

类似`cuckoo hashing`，从第一个开始找，如果$k$在$1-n$之间，就将对应位置的数字替换成$k$，表示替换正确。（之前考虑替换成$0$，出现本来就是$0$的问题）

最后从第一个位置找，第一个不符合要求的位置就是，否则为$n+1$。

不要忘记`nums[i] = i + 1`的死循环跳出判断。

代码：

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        int tmp = 0;
        for(int i = 0; i < n; i++) {
            if(nums[i] > 0 && nums[i] <= n) {
                tmp = nums[i];
                while(tmp > 0 && tmp <= n && nums[tmp - 1] != tmp) {
                    int t = tmp - 1;
                    tmp = nums[tmp - 1];
                    nums[t] = t + 1;
                }
            }
        }
        for(int i = 0; i < n; i++) {
            if(nums[i] != i + 1)
                return i + 1;
        }
        return n + 1;
    }
};
```



**15. 三数之和**

经典的`3-sum`问题。

题意：给一个整数数组，找和为0且不重复的三元组。

排序之后，从左开始双指针操作。

代码：

```cpp
class Solution {
public:
    int s[3100];
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int> > ans;
        for(int i = 0; i < n; i++)
            s[i] = nums[i];
        sort(s, s + n);
        for(int i = 0; i < n; i++) {
            if(i > 0 && s[i] == s[i - 1])continue;
            int l = i + 1, r = n - 1;
            while(l < r) {
                if(s[l] + s[r] + s[i] < 0) {
                    l++;
                }
                else if(s[l] + s[r] + s[i] > 0) {
                    r--;
                }
                else {
                    vector<int> v;
                    v.push_back(s[l]);
                    v.push_back(s[r]);
                    v.push_back(s[i]);
                    ans.push_back(v);
                    l++;
                    while(s[l] == s[l - 1] && l < r)l++;
                }
            }
        }
        return ans;
    }
};
```

另外，`vector`排序可使用：

```cpp
sort(nums.begin(), nums.end());
```

