# 583. Delete Operation for Two Strings (2021.05.31)

https://leetcode.com/problems/delete-operation-for-two-strings/

## Solution 1: Recursive

- $TC:O(2^max(m, n))$
- $SC:O(max(m, n))$
- m和n分别为word1和word2的长度，这里的复杂度与max(m, n)有关，是因为递归需要在较长的字符串对应的指针被遍历到0才结束。
- 本题要求的是最少删除多少个字符，使得删除后的word1和word2相同，换个角度来看，其实就是在求最长公共子序列（Longest common subsequence）。
- 假设word1和word2的LCS的长度为res，那么需要删除的最少字符数即为m + n - 2 * res。那要如何求出两个字符串的LCS呢？
- 首先，我们需要定义一个LCS(i, j)，这个method对应返回以word1[i-1]和word2[j-1]为结尾的的子字符串的LCS。
- 那么，当word1[i-1]等于word2[j-1]时，明显有LCS(i, j) = 1 + LCS(i - 1, j - 1)，即通过word1[0, i-2]以及word2[0, j-2]扩展一个字符得到。
- 如果word1[i-1]不等于word2[j-1]，那么LCS(i, j)就无法从更小的i，j对应的LCS进行扩展。
- 这时则有两种可能性，即LCS(i, j) = LCS(i, j - 1)[排除word2[j-1]]或是LCS(i, j) = LCS(i - 1, j)[排除word1[i-1]]，最终可以确定LCS(i, j) = max(LCS(i, j - 1), LCS(i - 1, j))

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        return m + n - 2 * LCS(word1, word2, m, n);
    }
    
    private int LCS(String word1, String word2, int i, int j) {
        if (i == 0 || j == 0) return 0;
        if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
            return LCS(word1, word2, i - 1, j - 1) + 1;
        } else {
            return Math.max(LCS(word1, word2, i, j - 1), LCS(word1, word2, i - 1, j));
        }
    }
}
```

## Solution 2: Memorization

- $TC:O(m*n)$
- $SC:O(m*n)$
- 很明显Solution1中存在大量重复的递归计算，我们可以memorization进行优化。

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] memo = new int[m + 1][n + 1];
        return m + n - 2 * LCS(word1, word2, m, n, memo);
    }
    
    private int LCS(String word1, String word2, int i, int j, int[][] memo) {
        if (i == 0 || j == 0) return 0;
        // 因为不排除有LCS(i, j)递归后计算出memo[i][j]等于0的情况
        // 在这里根据memo[i][j] != 0来判断其实还是会造成这一部分递归的重复计算
        // 如果想完全根除这些重复计算，可以初始化memo为-1或是用Integer来创建memo
        if (memo[i][j] != 0) return memo[i][j];
        
        if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
            memo[i][j] = LCS(word1, word2, i - 1, j - 1, memo) + 1;
        } else {
            memo[i][j] = Math.max(LCS(word1, word2, i, j - 1, memo), LCS(word1, word2, i - 1, j, memo));
        }
        
        return memo[i][j];
    }
}
```

## Solution 3: Bottom-up DP

- $TC:O(m*n)$
- $SC:O(m*n)$
- Solution2是Top-down的思路，我们也可以从Bottom-up入手。
- 我们用dp[i][j]表示以word1[i-1]和word2[j-1]结尾的子字符串对应的最大LCS。
- 不难发现，base cases为dp[i][0] = 0，dp[0][j] = 0。状态转移方程则为dp[i][j] = 1 + dp[i - 1][j - 1] 。(word1[i-1] = word2[j-1])
- 或是dp[i][j] = max(dp[i][j-1], dp[i-1][j])。(word1[i-1] != word2[j-1])

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) dp[i][j] = dp[i - 1][j - 1] + 1;
                else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return m + n - 2 * dp[m][n];
    }
}
```

## Solution 4: Bottom-up DP - another view

- $TC:O(m*n)$
- $SC:O(m*n)$
- 之前的解法都是从LCS的角度来考虑的，在该解法中，我们试着从编辑距离（edit distance）的角度入手。
- 我们用dp[i][j]表示以word1[i-1]和word2[j-1]结尾的子字符串对应需要删除的最小的字符数量。
- 那么可知base cases为dp[i][0] = i，dp[0][j] = j。
- 状态转移方程则为dp[i][j] = dp[i - 1][j - 1] 。(word1[i-1] = word2[j-1]，可以直接扩展相同部分而不需要额外删除)
- 或是dp[i][j] = min(dp[i][j-1], dp[i-1][j]) + 1。(word1[i-1] != word2[j-1]，需要考虑dp[i][j-1]且删除word2[j-1]以及dp[i-1][j]且删除word1[i-1]的较小者)

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i <= m; i++) {
            for (int j = 0; j <= n; j++) {
                if (i == 0 || j == 0) dp[i][j] = i + j;
                else if (word1.charAt(i - 1) == word2.charAt(j - 1)) dp[i][j] = dp[i - 1][j - 1];
                else dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + 1;
            }
        }
        return dp[m][n];
    }
}
```

## Solution 5: Optimized DP

- $TC:O(m*n)$
- $SC:O(n)$
- 从Solution4可以看出，dp[i][j]的值只取决于dp[i-1][j-1]（对角元素），dp[i-1][j]（上方元素）和dp[i][j-1]（左侧元素）。
- 因此，我们可以进一步压缩dp所占用的空间，用一个或是两个array来表示和迭代。

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[] dp = new int[n + 1];
        for (int j = 0; j <= n; j++) dp[j] = j;
        
        for (int i = 1; i <= m; i++) {
            int last = dp[0];
            dp[0] = i;
            for (int j = 1; j <= n; j++) {
                // 在更新dp[j]之前先保存起来，有可能会在j+1的计算中用到
                int cur = dp[j];
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) dp[j] = last;
                else dp[j] = Math.min(dp[j], dp[j - 1]) + 1;
                // 更新last，last始终是更新前的dp[j]
                last = cur;
            }
        }
        return dp[n];
    }
}
```