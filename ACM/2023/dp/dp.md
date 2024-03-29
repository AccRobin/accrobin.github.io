# 动态规划(DP)
$$
\text{陈志锐}
$$

---
## 什么是动态规划？

- 通过将原问题分解为相对简单的子问题来解决复杂问题
- 最优子结构，无后效性，子问题重叠
- 状态设计和状态转移

@@

## 例：数字三角形
> 给定一个 $r$ 行的数字三角形（$r \leq 1000$），需要找到一条从最高点到底部任意处结束的路径，使路径经过数字的和最大。每一步可以走到当前点左下方的点或右下方的点。
> ```
>        7 
>      3   8 
>    8   1   0 
>  2   7   4   4 
>4   5   2   6   5 
> ```
- 暴力方法 $O(2 ^ r)$
- 状态设计：令 $dp_{i, j}$ 表示走到第 $i$ 行第 $j$ 列的最小代价
- 转移方程：$dp[i][j] = min(dp[i - 1][j - 1], dp[i - 1][j]) + a[i][j]$
- $O(r ^ 2)$ 
@@

## 状态设计

- 状态设计即定义 $dp$ 的意义；一般可以从问题出发。如例题中，$dp_{i,j}$表示走到第 $i$ 行第 $j$ 个点的最小代价
- 状态设计还应当考虑初值的设置，例题中对应就是设置 $dp_{1,1}$ 的值
- 从一个暴力的状态开始，逐渐优化设计
- 从数据范围考虑，对数据范围敏感

@@

## 状态转移

- 顾名思义，就是考虑该状态如何转移过来
- 思考我们定义的状态应该如何求得，如例题中，第 $i$ 行 $j$ 列的位置只能由 $i - 1$ 行 $j$ 列和第 $i - 1$ 行 $j - 1$ 列走到
- 状态转移的设计依赖于状态的定义

@@

---

## 线性dp
- 线性DP是动态规划问题中的一类问题，指状态之间有线性关系的动态规划问题。
- 即存在一个线性的递推式，如上述例题中的数字三角形
- 定义过于抽象可以不用深究

@@ 

## 线性dp实例：最长上升子序列
> 这是一个简单的动规板子题。
>
> 给出一个由 $n(n\le 5000)$ 个不超过 $10^6$ 的正整数组成的序列。请输出这个序列的**最长上升子序列**的长度。
>
> 最长上升子序列是指，从原序列中**按顺序**取出一些数字排在一起，这些数字是**逐渐增大**的。

- 状态设计：从问题出发，要求的是最长上升子序列，可以定义 $dp_i$ 表示以 $a[i]$ 结尾的最长上升子序列，初值 $dp[1]$ 可以设为1
- 状态转移：要求 $dp_i$，很自然想到从前面选一个比它小的即可。$dp_i = \max\limits _{j = 1}^{i-1}(dp[j] + 1)(a[j] < a[i])$
```cpp
for (int i = 1; i <= n; i ++) {
    dp[i] = 1;
    for (int j = 1; j < i; j ++)
        if (a[j] < a[i]) dp[i] = max(dp[i], dp[j] + 1);  
}
```
@@

---

## 背包问题一：01背包
> 有 $n(n \leq 1000)$ 个物品和一个容量为 $W(W \leq 1000)$ 的背包，每个物品有重量 $w_{i}(w_{i} \leq W)$ 和价值 $v_{i}(v_{i} \leq 1e9)$ 两种属性，要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量。
- 暴力枚举，每个物品选或者不选，$O(2 ^ n)$ 
- 状态设计(要求什么？最大价值！)
- $dp_{i, j}$ 表示前 $i$ 个物品中选了重量为 $j$ 的物品最大价值，要求问题答案即 $dp[n][W]$
- 状态转移
- $dp[i][j] = max(dp[i - 1][j] + dp[i - 1][j - w[i]] + v[i])$

```cpp
for (int i = 1; i <= n; i ++) {
    for (int j = 0; j <= W; j ++) {
        dp[i][j] = dp[i - 1][j];
        if (j - w[i] >= 0) dp[i][j] = max(dp[i][j], dp[i - 1][j - w[i]] + v[i]);
    }
}
```

@@

---

## 背包问题二：完全背包
> 有 $n(n \leq 1000)$ 种物品和一个容量为 $W(W \leq 1000)$ 的背包，每种物品有重量 $w_{i}(w_{i} \leq W)$ 和价值 $v_{i}(v_{i} \leq 1e9)$ 两种属性，**个数无限**。要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量
- 状态设计与01背包一致($dp_{i, j})，难点在于如何转移
- 状态转移方程如何书写？
- 朴素方法：$dp[i][j] = \max\limits_{k=0}^{\infty}(dp[i - 1][j - k\times w[i]] + k \times v[i])$
- $dp[i][j] = max(dp[i - 1][j], dp[i][j - w[i]] + v[i])$

```cpp
for (int i = 1; i <= n; i ++) {
    for (int j = 0; j <= W; j ++) {
        dp[i][j] = dp[i - 1][j];
        if (j - w[i] >= 0) dp[i][j] = max(dp[i][j], dp[i][j - w[i]] + v[i]);
    }
}
```

@@

## 背包问题三：多重背包
> 有 $n(n \leq 1000)$ 种物品和一个容量为 $W(W \leq 1000)$ 的背包，每种物品有重量 $w_{i}(w_{i} \leq W)$ ，价值 $v_{i}(v_{i} \leq 1e9)$ 和**个数** $k_{i}(k_{i} \leq 1e9)$三种属性。要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量
- 本质上可以当做01背包，将每个物品拆开看成一种，共 $K(K = \sum\limits_{i = 1}^{n}k_i)$ 种。 
- 时间复杂度 $O(KW)$
- 二进制优化：将每种物品拆分成 $\log k_i$ 个物品, 第 $j$ 种物品拆分成的第 $i$ 个物品的重量为 $2^{i - 1}w_j$，价值为 $2^{i - 1}v_j$。时间复杂度 $O(nW\log k)$
- 拆分时多出来的部分怎么办？直接用即可。如6拆分成1，2，3；8拆分成1，2，4，1
- 原理：拆分出来的物品组合可以表示任意个数该种类物品
```cpp
vector<int> newW;
vector<int> newV;
for (int i = 1; i <= n; i ++) {
    for (int j = 1; j <= k[i]; j *= 2) {
        k[i] -= j;
        newW.push_back(j * w[i]);
        newV.push_back(j * v[i]);
    }
    if (k[i]) {
        newW.push_back(k[i] * w[i]);
        newV.push_back(k[i] * v[i]);
    }
}
```

@@

## 背包例题一：质数和分解
>任何大于 $1$ 的自然数 $n$ 都可以写成若干个大于等于 $2$ 且小于等于 $n$ 的质数之和表达式(包括只有一个数构成的和表达式的情况)，并且可能有不止一种质数和的形式。例如，$9$ 的质数和表达式就有四种本质不同的形式：
>
>$9 = 2 + 5 + 2 = 2 + 3 + 2 + 2 = 3 + 3 + 3 = 2 + 7$ 。
>
> 这里所谓两个本质相同的表达式是指可以通过交换其中一个表达式中参加和运算的各个数的位置而直接得到另一个表达式。
>
> 试编程求解自然数 $n(2 \leq n \leq 200)$ 可以写成多少种本质不同的质数和表达式。

- 可以发现 $n$ 相当于背包容量，质数个数相当于物品种类，质数的值相当于物品的重量
- 每个物品有无穷多种，所以相当于是一个完全背包问题
- 设状态 $dp_{i,j}$ 表示前 $i$ 种质数中和 $j$ 的本质不同质数和表达式数量
- 状态转移 $dp_{i, j} = dp_{i - 1, j} + dp_{i, j - w[i]}$
- tips: 多组数据最好提前预处理出所有质数，以免被卡

@@

---

## 区间dp
- 区间dp是线性dp的拓展。顾名思义，区间dp的划分一般以一段区间的待求解值作为状态
- $dp_{i, j}$ 一般表示为从 $i$ 到 $j$ 内的带求解值
- 多适用于可以合并的问题。
- 状态转移一般会枚举合并点，将问题分为两个部分，通过合并两个部分的最优值得到原问题的最优值
- 状态转移方程多是以下形式 $dp_{i, j} = \max\limits _{k = i} ^ {j - 1}f(dp_{i, k}, dp_{k + 1, j})$

@@ 

## 区间dp实例1：石子合并（弱化版）
> 设有 $N(N \le 300)$ 堆石子排成一排，其编号为 $1,2,3,\cdots,N$。每堆石子有一定的质量 $m_i\ (m_i \le 1000)$。现在要将这 $N$ 堆石子合并成为一堆。每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻。合并时由于选择的顺序不同，合并的总代价也不相同。试找出一种合理的方法，使总的代价最小，并输出最小代价。

- 区间dp模板题
- 考虑 $dp_{i, j}$ 表示合并区间 $i$ 到 $j$ 的最小代价
- 合并时只需枚举最后合并的两堆是以哪个中心点划分即可
- 状态转移方程 $dp_{i, j} = \min\limits _{k = i}^{j - 1}(dp_{i, k} + dp_{k + 1, j} + w_{i, k} + w_{k + 1, j})$
- 使用前缀和可以快速算出 $w_{i, k}$ 和 $w_{k + 1, j}$
- 时间复杂度 $O(n^3)$

@@

## 区间dp实例2：石子合并
> 在一个圆形操场的四周摆放 $N(N \leq 100)$ 堆石子，每堆石子有 $a_i(a_i \leq 20)$ 个。现要将石子有次序地合并成一堆，规定每次只能选相邻的 $2$ 堆合并成新的一堆，并将新的一堆的石子数，记为该次合并的得分。
>
> 试设计出一个算法,计算出将 $N$ 堆石子合并成 $1$ 堆的最小得分和最大得分。

- 与上一题有些许不同，上一题排列成一排，这一题围成一圈
- 其实只需将石子复制一遍后放在末尾排列即可。如 1 3 2 变成 1 3 2 1 3 2
- 这样就变成了上一个问题，最终问题的答案等于 $\min\limits _{i = 1}^{n} f_{i, i + n - 1}$ 和 $\max\limits _{i = 1}^{n} g_{i, i + n - 1}$
- 时间复杂度 $O(n^3)$