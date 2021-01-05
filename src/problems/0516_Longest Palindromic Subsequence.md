## 516. Longest Palindromic Subsequence (2020.12.27)

https://leetcode.com/problems/longest-palindromic-subsequence/

## Related Questions:
- 5. Longest Palindromic Substring

## Solution 1: DP

- $TC:O(N^2)$
- $SC:O(N^2)$
- 首先需要明确subsequence和substring是不同的，主要差别在于subsequence可以是不连续的。
- 因此，我们可以定义dp[i][j]来表示字符串s(i,j)所包含的最长回文子序列的长度。则存在状态转移方程：若s(i)和s(j)相等，则dp[i][j]=dp[i+1][j-1]+2，即s(i,j)所包含的最长回文子序列加上两个端点的字符。
- 若s(i)不等于s(j)，则dp[i][j]=max(dp[i][j-1], dp[i+1][j])。此处不能简单地将dp[i][j]=dp[i+1][j-1]，是因为可能存在某侧端点的字符和s(i,j-1)或s(i+1,j)的子序列构成回文子序列的情况，比如 `...[a]...ba...[c]...`，其中 `[]` 表示新加入判断的两侧字符，当然dp[i][j-1]和dp[i+1][j]在之前的遍历中也已计算出来了。

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        if (s == null) return 0;
        
        int n = s.length();
        int[][] dp = new int[n][n];
        
        for (int i = n - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i + 1; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                } else {
                    dp[i][j] = Math.max(dp[i][j - 1], dp[i + 1][j]);
                }
            }
        }
        
        return dp[0][n - 1];
    }
}
```

## Solution 2: Recursion

- $TC:O(N^2)$
- $SC:O(N^2)$
- 注意在递归时进行记忆化处理，避免重复计算。

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        if (s == null) return 0;
        
        int n = s.length();
        int[][] mem = new int[n][n];
        return helper(s, 0, n - 1, mem);
    }
    
    private int helper(String s, int i, int j, int[][] mem) {
        if (mem[i][j] != 0) return mem[i][j];
        
        if (i > j) return 0;
        if (i == j) return 1;
        
        if (s.charAt(i) == s.charAt(j)) {
            mem[i][j] = helper(s, i + 1, j - 1, mem) + 2;
        } else {
            mem[i][j] = Math.max(helper(s, i, j - 1, mem), helper(s, i + 1, j, mem));
        }
        
        return mem[i][j];
    }
}
```

