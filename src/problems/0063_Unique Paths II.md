## 63. Unique Paths II (2020.12.31)

https://leetcode.com/problems/unique-paths-ii/

## Related Questions:
- 62. Unique Paths

## Solution 1: DP

- $TC:O(M*N)$
- $SC:O(M*N)$
- 本题思路与Leetcode.62相似，不同的地方在于有的格子可能存在黑曜石，这种情况下该格子对应的unique path数量为0。同样地，dp解法的空间复杂度可以进一步优化，即二维数组 -> 两个一维数组 -> 一个一维数组。同时，如果计算出的某行/某列的unique path均为0，即该行/该列的每个格子，或者是自身存在黑曜石，或者是上方和左侧受到黑曜石影响，这种情况可以用flag标记来提前结束遍历。

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        
        for (int i = 0; i < m; i++) {
            if (obstacleGrid[i][0] == 1) break;
            dp[i][0] = 1;
        }
        
        for (int j = 0; j < n; j++) {
            if (obstacleGrid[0][j] == 1) break;
            dp[0][j] = 1;
        }
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) dp[i][j] = 0;
                else dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        
        return dp[m - 1][n - 1];
    }
}
```

## Solution 2: Recursion

- $TC:O(M*N)$
- $SC:O(M*N)$

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        int[][] memo = new int[m][n];
        return uniquePathsWithObstacles(obstacleGrid, m - 1, n - 1, memo);
    }
    
    private int uniquePathsWithObstacles(int[][] obstacleGrid, int m, int n, int[][] memo) {
        if (m < 0 || n < 0 || obstacleGrid[m][n] == 1) return 0;
        // 没有黑曜石的情况下，可以是m == 0 || n == 0
        // 但本题要考虑到黑曜石的影响，必须递归到起始点
        if (m == 0 && n == 0) return 1;
        if (memo[m][n] != 0) return memo[m][n];
        
        memo[m][n] = uniquePathsWithObstacles(obstacleGrid, m - 1, n, memo) +
            uniquePathsWithObstacles(obstacleGrid, m, n - 1, memo);
        return memo[m][n];
    }
}
```

