# 746. Min Cost Climbing Stairs (2021.01.23)

https://leetcode.com/problems/min-cost-climbing-stairs/


## Solution 1: DP

- $TC:O(n)$
- $SC:O(n)$
- dp[i]表示到达第i层阶梯所需要的最小cost，因为可以一次跳一个阶梯或两个阶梯，则有状态转移方程dp[i] = cost[i] + min(dp[i-1], dp[i-2])。
- 初始状态可以从第0层或第1层开始，即dp[0] = cost[0], dp[1] = cost[1]。
- 最后需要注意的是dp[n-1]并不是top层，top层的min cost是由dp[n-1]和dp[n-2]决定的。

```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        if (cost == null || cost.length == 0) return 0;
        
        int n = cost.length;
        int[] dp = new int[n];
        dp[0] = cost[0];
        dp[1] = cost[1];
        
        for (int i = 2; i < n; i++) {
            dp[i] = Math.min(dp[i - 2], dp[i - 1]) + cost[i];
        }
        
        return Math.min(dp[n - 2], dp[n - 1]);
    }
}
```

## Solution 2: DP optimized

- $TC:O(n)$
- $SC:O(1)$
- dp[n]只由dp[n-1]和dp[n-2]决定，相当于斐波那契数列。

```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int n = cost.length;
        int cn_2 = cost[0], cn_1 = cost[1], cn = 0;
        
        for (int i = 2; i < n; i++) {
            cn = cost[i] + Math.min(cn_1, cn_2);
            cn_2 = cn_1;
            cn_1 = cn;
        }
        
        return Math.min(cn_1, cn_2);
    }
}
```