# 1035. Uncrossed Lines (2021.06.01)

https://leetcode.com/problems/uncrossed-lines/

## Related Questions:
- 583. Delete Operation for Two Strings

## Solution 1: DP

- $TC:O(m*n)$
- $SC:O(m*n)$
- 其实就是求Longest Common Subsequence

```java
class Solution {
    public int maxUncrossedLines(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (nums1[i - 1] == nums2[j - 1]) dp[i][j] = 1 + dp[i - 1][j - 1];
                else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[m][n];
    }
}
```