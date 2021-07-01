# 322. Coin Change (2021.06.02)

https://leetcode.com/problems/coin-change/

## Solution 1: DP ver1

- $TC:O(m*n)$
- $SC:O(m)$
- m为amount的值，n为coins的种类数。由于本题的coins最大只有12种，所以时间复杂度也可以看作O(m)。
- 我们用dp[i]表示当amount为i时所需要的最少coins数，那么就存在状态转移方程dp[i] = min(dp[i-coins[j]] + 1)，0 <= j <= coins.length。
- 即amount=i对应的最少coins数为amount=i-某种面值的coin所对应的最少coins数中的最小者加上1。
- 由于当coin的面值大于amount时属于无法通过coins组成amount的情况，需要返回-1，而这又会影响到min的比较。
- 对于这个问题，我们用amount+1来标记这类不可能的情况，在最后返回的时候进行检查，看是否返回-1。

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        // amount + 1就是一个在当前amount下肯定无法达到的最大count
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                // 如果i没有比coin来得大，则不必进行迭代
                if (i >= coin) {
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

## Solution 2: DP ver2

- $TC:O(m*n)$
- $SC:O(m)$
- 以遍历coins作为外循环，可以节省一些最初coin的值比amount来得大的循环 - sum(coins)。

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

## Solution 3: Memorization

- $TC:O(m*n)$
- $SC:O(m)$
- Top-down解法，我们用coinChange(amount)计算amount所对应的最小coins数，那么其就会等于coinChange(amount-coin)中的最小者加上1。

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] memo = new int[amount + 1];
        return coinChange(coins, amount, memo);
    }
    
    private int coinChange(int[] coins, int amount, int[] memo) {
        if (amount < 0) return -1; // 剩余的amount已经不足，属于无法用coins组成amount的情况
        if (amount == 0) return 0; // 恰好组成了amount
        if (memo[amount] != 0) return memo[amount];
        
        int minCount = Integer.MAX_VALUE;
        for (int coin : coins) {
            int res = coinChange(coins, amount - coin, memo);
            // 只有当返回的res不小于0时才更新minCount
            if (res >= 0) minCount = Math.min(minCount, 1 + res);
        }
        // 有可能minCount没有被更新，仍然是Integer.MAX_VALUE
        memo[amount] = minCount == Integer.MAX_VALUE ? -1 : minCount;
        return memo[amount];
    }
}
```