## 64. Minimum Path Sum (2020.12.31)

https://leetcode.com/problems/minimum-path-sum/

## Related Questions:
- 62. Unique Paths

## Solution 1: DP

- $TC:O(M*N)$
- $SC:O(M*N)$
- dp[i][j]表示到达grid[i][j]的最小路径和，有状态转移方程: dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[m][n];
        
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; i++) dp[i][0] = grid[i][0] + dp[i - 1][0];
        for (int j = 1; j < n; j++) dp[0][j] = grid[0][j] + dp[0][j - 1];
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = grid[i][j] + Math.min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        
        return dp[m - 1][n - 1];
    }
}
```

## Solution 2: DP -optimized

- $TC:O(M*N)$
- $SC:O(N)$

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[] dp = new int[n];
        
        dp[0] = grid[0][0];
        for (int j = 1; j < n; j++) dp[j] = dp[j - 1] + grid[0][j];
        
        for (int i = 1; i < m; i++) {
            dp[0] += grid[i][0];
            for (int j = 1; j < n; j++) {
                dp[j] = grid[i][j] + Math.min(dp[j - 1], dp[j]);
            }
        }
        
        return dp[n - 1];
    }
}
```

