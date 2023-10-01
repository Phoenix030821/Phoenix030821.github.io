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



感叹一下，自己的水平下滑太多了。复健之路漫漫，虚心学习，不要有畏难之心。



#### 41. 缺失的第一个正数

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



#### 15. 三数之和

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



#### 2801. 统计范围内的步进数字数目

第356场周赛题。

如果一个整数相邻数位之间差的绝对值都恰好是`1`，那么这个数字被称为步进数字。

给你两个正整数 `low` 和 `high` ，都用字符串表示，统计闭区间 `[low, high]` 内的步进数字数目。对`1e9 + 7`取模。

数位DP模板题。

首先预处理，`f[i][j]`表示最高位是第`i`位，且为`j`的方案数。

然后设置函数`dp(i)`表示从`1`到`i`的方案数，用`dp(high) - dp(low)`算。单独处理以下`low`是否为步进数字。

将数位从最高位开始枚举，从`0`到`i - 1`的该位，如果和`x`高一位的数相差为1，则加上`f[t - i][j]`，表示前几位和`x`相同的结果数。最后加上不到`x`的位数的方案数。

取模时要注意！！两个取模之后的数相减可能成为负数，要让被减数先加`MOD`，周赛两行泪。

```cpp
class Solution {
public:
    long long f[110][10];
    int MOD = 1e9 + 7;
    long long dp(string s) {
        vector<int> num;
        int t = s.length();
        for(int i = 0; i < t; i++)
            num.push_back(s[i] - '0');
        long long ans = 0;
        int last = -1;
        for(int i = 0; i < t; i++){
            int x = num[i];
            for(int j = (i == 0); j < x; j++){
                if(abs(j - last) == 1 || i == 0){
                    ans += f[t - i][j];
                    ans %= MOD;
                }
            }
            if(abs(x - last) != 1 && i != 0) break;
            last = x;
            if(i == t - 1) {
                ans ++;
                ans %= MOD;
            }
        }

        for(int i = 1; i < t; i++){
            for(int j = 1; j < 10;j ++){
                ans += f[i][j];
                ans %= MOD;
            }
        }
        return ans;
    }
    int countSteppingNumbers(string low, string high) {
        int l1 = low.length();
        int l2 = high.length();
        int l = l2;
        for(int i = 0; i < 10; i++)
            f[1][i] = 1;
        for(int i = 2; i <= l + 2; i++) {
            for(int j = 1; j < 9; j++) {
                f[i][j] = f[i - 1][j - 1] + f[i - 1][j + 1];
                f[i][j] %= MOD;
            }
            f[i][0] = f[i - 1][1];
            f[i][9] = f[i - 1][8];
        }
        long long aa = dp(high);
        aa += 1e9 + 7;
        aa -= dp(low);
        aa %= MOD;
        for(int i = 1; i < l1; i++) {
            if(abs(low[i] - low[i - 1])!= 1)
                return aa;
        }
        aa += 1;
        aa %= MOD;
        return aa;
    }
};
```



#### 126. 单词接龙II

按字典 `wordList` 完成从单词 `beginWord` 到单词 `endWord` 转化，一个表示此过程的 **转换序列** 是形式上像 `beginWord -> s1 -> s2 -> ... -> sk` 这样的单词序列，并满足：

- 每对相邻的单词之间仅有单个字母不同。
- 转换过程中的每个单词 `si`（`1 <= i <= k`）必须是字典 `wordList` 中的单词。注意，`beginWord` 不必是字典 `wordList` 中的单词。
- `sk == endWord`

给你两个单词 `beginWord` 和 `endWord` ，以及一个字典 `wordList` 。请你找出并返回所有从 `beginWord` 到 `endWord` 的 **最短转换序列** ，如果不存在这样的转换序列，返回一个空列表。每个序列都应该以单词列表 `[beginWord, s1, s2, ..., sk]` 的形式返回。



在相邻的两个单词之间建边，直接枚举相邻的所有单词，看其是否在单词表中，比二维循环更快。

bfs找最短距离，dfs找路径。

从起点开始找，尝试两次剪枝，均TLE。

dfs找路径的优化策略：直接从终点开始找，然后找该点周围，`dis`为该点的`dis`减1的点，放入队列中存储。

这样不仅是剪枝，直接将所有无用情况全部排除。

```cpp
class Solution {
public:
    string wl[510];
    vector<vector<string> > ans;
    unordered_map<string, int> mp;
    int head[510], num, maxm, en;
    int dis[510], vis[510];
    int st[510];
    struct edge {
        int to, nxt;
    }p[20010];
    inline void add(int from, int too) {
        p[++num].to = too;
        p[num].nxt = head[from];
        head[from] = num;
    }
    void print() {
        int l = dis[en];
        vector<string> v;
        for(int i = 0; i <= l; i++) {
            v.push_back(wl[st[i]]);
        }
        ans.push_back(v);
    }
    void dfs(int i) {
        if(dis[i] == 0) {
            print();
            return;
        }
        st[dis[i]] = i;
        for(int t = head[i]; t; t = p[t].nxt) {
            if(dis[p[t].to] == dis[i] - 1) {
                dfs(p[t].to);
            }
        }
        return;
    }
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        
        int l = beginWord.length();
        int n = wordList.size();
        for(int i = 0; i <= n + 1; i++) {
            head[i] = 0;
        }
        for(int i = 0; i < n; i++) {
            mp[wordList[i]] = i + 1;
            wl[i + 1] = wordList[i];
        }
        wl[n + 1] = beginWord;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < l; j++) {
                for(int k = 0; k < 26; k++) {
                    if(wordList[i][j] != 'a' + k) {
                        string rep = wordList[i];
                        rep[j] = 'a' + k;
                        if(mp.count(rep)) {
                            add(i + 1, mp[rep]);
                        }
                    }
                }
            }
        }
        for(int j = 0; j < l; j++) {
            for(int k = 0; k < 26; k++) {
                if(beginWord[j] != 'a' + k) {
                    string rep = beginWord;
                    rep[j] = 'a' + k;
                    if(mp.count(rep)) {
                        add(n + 1, mp[rep]);
                        add(mp[rep], n + 1);
                    }
                }
            }
        }
        for(int i = 0; i <= n; i++) {
            vis[i] = 0;
            dis[i] = 0x3f3f3f3f;
        }
        
        queue<int> q;
        q.push(n + 1);
        vis[n + 1] = 1;
        dis[n + 1] = 0;
        en = mp[endWord];
        while(!q.empty()) {
            int t = q.front();
            q.pop();
            for(int i = head[t]; i; i = p[i].nxt) {
                if(!vis[p[i].to]) {
                    dis[p[i].to] = dis[t] + 1;
                    q.push(p[i].to);
                    vis[p[i].to] = 1;
                }
            }
        }
        maxm = dis[en];
        if(dis[en] == 0x3f3f3f3f) {
            return ans;
        }
        for(int i = 0; i <= n + 1; i++) {
            vis[i] = 0;
        }
        st[0] = n + 1;
        dfs(en);
        return ans;
    }
};
```



#### 2791. 树中可以形成回文的路径数



```cpp
class Solution {
public:
    int f[100100];
    bool vis[100100];
    vector<int> pp;
    string ss;
    int bitcount(int i) {
        int num = 0;
        while(i) {
            num += (i % 2);
            i /= 2;
        }
        return num;
    }
    void dfs(int i) {
        if(vis[i]) return;
        dfs(pp[i]);
        f[i] = f[pp[i]] ^ (1 << (ss[i] - 'a'));
        vis[i] = 1;
        return;
    }
    long long countPalindromePaths(vector<int>& parent, string s) {
        int n = parent.size();
        for(int i = 0; i < n; i++)
            pp.push_back(parent[i]);
        ss = s;
        f[0] = 0;
        vis[0] = 1;
        for(int i = 1; i < n; i++) {
            if(!vis[i]) dfs(i);
        }
        unordered_map<int, int> mp;
        // for(int i = 0; i < n; i++) {
        //     if(!mp.count(f[i])) {
        //         mp[f[i]] = 1;
        //     }
        //     else {
        //         mp[f[i]]++;
        //     }
        // }
        long long sum = 0;
        for(int i = 0; i < n; i++) {
            if(mp.count(f[i])) {
                sum += mp[f[i]];
            }
            for(int j = 0; j < 26; j++) {
                if(mp.count(f[i] ^ (1 << j))) {
                    sum += mp[f[i] ^ (1 << j)];
                }
            }
            mp[f[i]]++;
        }
        return sum;
    }
};
```

