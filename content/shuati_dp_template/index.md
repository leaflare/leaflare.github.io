+++
title = "Dp template"
date = 2021-04-11 17:21:06
slug = "202104111721"

[taxonomies]
tags = ["Cram" ]
categories = ["Cram"]

+++

动态规划

<!-- more -->

动态规划问题的一般形式是穷举求最值，比如最长递增子序列，最小距离。

**存在「重叠子问题」**：如果暴力穷举效率会极其低下，所以需要「备忘录」或者「DP table」来优化穷举过程，避**存在「最优子结构」**：才能通过子问题的最值得到原问题的最值。

**找到「状态」 -> 定义 dp 数组/函数 -> 明确「选择」-> 明确 base case。**

## 斐波那契数列

### 暴力递归

```c++
int fib(int N) {
    if (N == 1 || N == 2) return 1;
    return fib(N - 1) + fib(N - 2);
}
```

时间复杂度： O(2^n)

### 带备忘录的递归解法

```c++
int fib(int N) {
    if (N < 1) return 0;
    // 备忘录全初始化为 0
    vector<int> memo(N + 1, 0);
    // 初始化最简情况
    return helper(memo, N);
}

int helper(vector<int>& memo, int n) {
    // base case 
    if (n == 1 || n == 2) return 1;
    // 已经计算过
    if (memo[n] != 0) return memo[n];
    memo[n] = helper(memo, n - 1) + 
                helper(memo, n - 2);
    return memo[n];
}
```

时间复杂度： O(n)

### dp 数组的迭代解法

```c++
int fib(int N) {
    vector<int> dp(N + 1, 0);
    // base case
    dp[1] = dp[2] = 1;
    for (int i = 3; i <= N; i++)
        dp[i] = dp[i - 1] + dp[i - 2];
    return dp[N];
}
```

时间复杂度： O(n)

### 空间复杂度降为 O(1)

根据斐波那契数列的状态转移方程，当前状态只和之前的两个状态有关，只要存储之前的两个状态就可以了。

```c++
int fib(int n) {
    if (n == 2 || n == 1) 
        return 1;
    int prev = 1, curr = 1;
    for (int i = 3; i <= n; i++) {
        int sum = prev + curr;
        prev = curr;
        curr = sum;
    }
    return curr;
}
```

## 凑零钱问题

#### 题目

`k`种面值的硬币，面值分别为`c1, c2 ... ck`，每种硬币的数量无限，再给一个总金额`amount`，问最少需要几枚硬币凑出这个金额，如果不可能凑出，算法返回 -1 。

#### 思路

**「状态」**：目标金额`amount`。

**`dp`函数的定义**：函数 dp(n)表示，当前的目标金额是`n`，至少需要`dp(n)`个硬币凑出该金额。

**「选择」并择优**，也就是对于每个状态，可以做出什么选择改变当前状态。具体到这个问题，无论当的目标金额是多少，选择就是从面额列表`coins`中选择一个硬币，然后目标金额就会减少

**base case**，目标金额为 0 时，所需硬币数量为 0；当目标金额小于 0 时，无解，返回 -1

### 暴力解法

```python
# 伪码框架
def coinChange(coins: List[int], amount: int):
    # 定义：要凑出金额 n，至少要 dp(n) 个硬币
    def dp(n):
        # 做选择，需要硬币最少的那个结果就是答案
        for coin in coins:
            res = min(res, 1 + dp(n - coin))
        return res
    # 我们要求目标金额是 amount
    return dp(amount)

```

```python
def coinChange(coins: List[int], amount: int):

    def dp(n):
        # base case
        if n == 0: return 0
        if n < 0: return -1
        # 求最小值，所以初始化为正无穷
        res = float('INF')
        for coin in coins:
            subproblem = dp(n - coin)
            # 子问题无解，跳过
            if subproblem == -1: continue
            res = min(res, 1 + subproblem)

        return res if res != float('INF') else -1

    return dp(amount)
```

**时间复杂度：子问题总数 x 解决每个子问题的时间**。

子问题总数为递归树节点个数，是 O(n^k)。每个子问题中含有一个 for 循环，复杂度为 O(k)。所以总时间复杂度为 O(k * n^k)，指数级别。

### 带备忘录的递归

通过备忘录消除子问题

```python
def coinChange(coins: List[int], amount: int):
    # 备忘录
    memo = dict()
    def dp(n):
        # 查备忘录，避免重复计算
        if n in memo: return memo[n]

        if n == 0: return 0
        if n < 0: return -1
        res = float('INF')
        for coin in coins:
            subproblem = dp(n - coin)
            if subproblem == -1: continue
            res = min(res, 1 + subproblem)

        # 记入备忘录
        memo[n] = res if res != float('INF') else -1
        return memo[n]

    return dp(amount)
```

子问题总数不会超过金额数 n，即子问题数目为 O(n)。处理一个子问题的时间不变，仍是 O(k)，所以总的时间复杂度是 O(kn)。

### dp 数组的迭代解法

`dp[i] = x`表示，当目标金额为`i`时，至少需要`x`枚硬币。

```c++
int coinChange(vector<int>& coins, int amount) {
    // 数组大小为 amount + 1，初始值也为 amount + 1
    vector<int> dp(amount + 1, amount + 1);
    // base case
    dp[0] = 0;
    for (int i = 0; i < dp.size(); i++) {
        // 内层 for 在求所有子问题 + 1 的最小值
        for (int coin : coins) {
            // 子问题无解，跳过
            if (i - coin < 0) continue;
            dp[i] = min(dp[i], 1 + dp[i - coin]);
        }
    }
    return (dp[amount] == amount + 1) ? -1 : dp[amount];
}
```

`dp`数组初始化为`amount + 1`，是因为凑成`amount`金额的硬币数最多只可能等于`amount`（全用 1 元面值的硬币），所以初始化为`amount + 1`就相当于初始化为正无穷，便于后续取最小值。
