# 712. Minimum ASCII Delete Sum for Two Strings (2021.05.31)

https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/

## Similar Questions:
- 583. Delete Operation for Two Strings

## Solution 1: DP

- $TC:O(m*n)$
- $SC:O(m*n)$
- 本题其实和LC583是一样的解法，只不过在这里cost不再是删除的字符数量（每个字符消耗1），而是被删除字符对应的ASCII值（每个字符消耗对应的ASCII值）。
- 同样地，该DP解法也可以进一步压缩使用的空间复杂度。

```java
class Solution {
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) dp[i][0] = dp[i - 1][0] + s1.charAt(i - 1);
        for (int j = 1; j <= n; j++) dp[0][j] = dp[0][j - 1] + s2.charAt(j - 1);
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = dp[i - 1][j - 1];
                else dp[i][j] = Math.min(dp[i - 1][j] + s1.charAt(i - 1), dp[i][j - 1] + s2.charAt(j - 1));
            }
        }
        
        return dp[m][n];
    }
}
```