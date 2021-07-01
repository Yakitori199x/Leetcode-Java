# 120. Triangle (2021.06.01)

https://leetcode.com/problems/triangle/

## Solution 1: Memorization

- $TC:O(n^2)$
- $SC:O(n^2)$
- n为triangle对应的层数。
- minimumTotal(r, c)会计算并返回以第r层，第c个索引处的点为顶点的triangle的minimum path sum。
- 那么就存在minimumTotal(r, c) = min(minimumTotal(r + 1, c), minimumTotal(r + 1, c + 1)) + triangle[r][c]。

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        Integer[][] memo = new Integer[triangle.size()][triangle.size()];
        return minimumTotal(triangle, 0, 0, memo);
    }
    
    private int minimumTotal(List<List<Integer>> triangle, int r, int c, Integer[][] memo) {
        if (r == triangle.size()) return 0;
        if (memo[r][c] != null) return memo[r][c];
        
        memo[r][c] = triangle.get(r).get(c) + Math.min(minimumTotal(triangle, r + 1, c, memo), minimumTotal(triangle, r + 1, c + 1, memo));
        return memo[r][c];
    }
}
```

## Solution 2: DP

- $TC:O(n^2)$
- $SC:O(n^2)$
- 由Solution1可以看出bottom-up解法的base case，即对于以最底层的每个点为顶点构成的triangle，其minimum path sum就是该点所对应的值。
- 所以，我们用dp[i][j]表示以第i层，第j个索引处的点为顶点的triangle的minimum path sum。
- 那么，存在状态转移方程dp[i][j] = triangle[i][j] + min(dp[i + 1][j], dp[i + 1][j + 1])，且base case为dp[n - 1][*] = triangle[n - 1][*]。

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[][] dp = new int[n][n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                if (i == n - 1) dp[i][j] = triangle.get(i).get(j);
                else dp[i][j] = triangle.get(i).get(j) + Math.min(dp[i + 1][j], dp[i + 1][j + 1]);
            }
        }
        return dp[0][0];
    }
}
```

## Solution 3: DP - optimized

- $TC:O(n^2)$
- $SC:O(n)$
- 不难发现，Solution2的二维DP实际上就是从下往上一层一层地递推出最终结果dp[0][0]，且上面一层的结果只直接依赖于其下面的一层结果。
- 因此我们可以进一步优化空间开销。

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[] dp = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                // n-1的情况其实可以去掉，不过要多分配一个位置给dp，即dp = new int[n + 1]
                // 因为相当于在原triangle的基础上添加一层
                if (i == n - 1) dp[j] = triangle.get(i).get(j);
                else dp[j] = triangle.get(i).get(j) + Math.min(dp[j], dp[j + 1]);
            }
        }
        return dp[0];
    }
}
```