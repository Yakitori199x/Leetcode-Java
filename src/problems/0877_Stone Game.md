# 877. Stone Game (2021.05.17)

https://leetcode.com/problems/stone-game/

## Similar Questions:
- 486. Predict the Winner

## Solution 1: DP (Memorization)

- $TC:O(n^2)$
- $SC:O(n^2)$
- 解法和LC486一致，不过因为本题中piles的数量始终为偶数，所以先手的玩家一定可以获胜，直接返回true。如果piles的数量为奇数这不适用。
- 其次，如果我们只是要判断是否先手能赢，可以使用这种选法。但是，如果我们需要知道两个玩家的分差，那么这种选法就无效了，因为这并不是对先手玩家来说的最优选法。

```java
class Solution {
    public boolean stoneGame(int[] piles) {
        int n = piles.length;
        int[][] memo = new int[n][n];
        return stoneGame(piles, 0, n - 1, memo) > 0;
    }
    
    private int stoneGame(int[] piles, int s, int e, int[][] memo) {
        if (s > e) return 0;
        if (memo[s][e] != 0) return memo[s][e];
        int takeFirst = piles[s] - stoneGame(piles, s + 1, e, memo);
        int takeLast = piles[e] - stoneGame(piles, s, e - 1, memo);
        memo[s][e] = Math.max(takeFirst, takeLast);
        return memo[s][e];
    }
}
```

## Solution 2: DP

- $TC:O(n^2)$
- $SC:O(n^2)$
- 同样地，可以将二维DP压缩成一维DP，这里就不详细赘述了。

```java
class Solution {
    public boolean stoneGame(int[] piles) {
        int n = piles.length;
        int[][] dp = new int[n][n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (i == j) dp[i][j] = piles[i];
                else dp[i][j] = Math.max(piles[i] - dp[i + 1][j], piles[j] - dp[i][j - 1]);
            }
        }
        return dp[0][n - 1] > 0;
    }
}
```