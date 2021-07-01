# 1690. Stone Game VII (2021.05.20)

https://leetcode.com/problems/stone-game-vii/

## Solution 1: DP (Memorization)

- $TC:O(N^2)$
- $SC:O(N^2)$
- 实际上本题的解法和LC877一致，只不过这里是取了stones[i]，获得的是剩余部分stones的和作为本轮得分。

```java
class Solution {
    public int stoneGameVII(int[] stones) {
        int n = stones.length, leftSum = 0;
        for (int stone : stones) leftSum += stone;
        int[][] memo = new int[n][n];
        return stoneGameVII(stones, leftSum, 0, n - 1, memo);
    }
    
    private int stoneGameVII(int[] stones, int leftSum, int s, int e, int[][] memo) {
        if (s >= e) return 0;
        if (memo[s][e] != 0) return memo[s][e];
        
        int chooseLeft = leftSum - stones[s] - stoneGameVII(stones, leftSum - stones[s], s + 1, e, memo);
        int chooseRight = leftSum - stones[e] - stoneGameVII(stones, leftSum - stones[e], s, e - 1, memo);
        memo[s][e] = Math.max(chooseLeft, chooseRight);
        return memo[s][e];
    }
}
```

## Solution 2: DP

- $TC:O(N^2)$
- $SC:O(N^2)$
- 状态转移方程dp[i][j] = max(sum[i+1...j] - dp[i+1][j], sum[i...j-1] - dp[i][j - 1])。
- sum[i...j]为stones[i]到stones[j]这段区间的和，可以通过前缀和快速地获取。

```java
class Solution {
    public int stoneGameVII(int[] stones) {
        int n = stones.length;
        int[] pre = new int[n + 1];
        int[][] dp = new int[n][n];
        
        for (int i = 0; i < n; i++) pre[i + 1] = pre[i] + stones[i];
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                dp[i][j] = Math.max(
                    pre[j + 1] - pre[i + 1] - dp[i + 1][j], 
                    pre[j] - pre[i] - dp[i][j - 1]
                );
            }
        }
        return dp[0][n - 1];
    }
}
```