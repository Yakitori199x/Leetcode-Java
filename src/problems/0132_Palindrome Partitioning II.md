# 132. Palindrome Partitioning II (2021.02.18)

https://leetcode.com/problems/palindrome-partitioning-ii/

## Solution 1: DP

- $TC:O(N^2)$
- $SC:O(N^2)$
- 和LC131相比，本题并不需要返回所有可能的palindrome partition，只需要返回最小的切分数，通常来说就是使用DP求解。
- 这里我们用到两个dp数组，minCuts[i]表示s(0,i)对应的最小切分数，isPalindrome[j][i]则是经常遇到的，表示s(j,i)是否是回文串。
- 不难发现，当i增加时，新加入的字符s[i]到前一字符串s(0,i-1)中的任意字符s[j]的子串都有可能是回文串。
- 比如s[i]到s[0]之间构成了回文串，那么最小切分数就是0。s[i]和s[j]之间构成回文串，那么最小切分就是minCuts[j-1]+1。
- 所以，状态转移方程为：minCuts[i] = min(0, minCuts[0] + 1, ..., minCuts[j-1] + 1, ..., minCuts[i-1] + 1)
- 条件为s(j,i)为回文串且0<=j<=i，minCuts[i]的初始值为i，同时也是最大值，因为每个字符串必定能被切分成N-1个单字符partition。

```java
class Solution {
    public int minCut(String s) {
        if (s == null || s.length() <= 1) return 0;
        
        int n = s.length();
        int[] minCuts = new int[n];
        boolean[][] isPalindrome = new boolean[n][n];
        
        for (int i = 0; i < n; i++) {
            // 记得在递推之前初始化minCuts[i]
            minCuts[i] = i;
            for (int j = 0; j <= i; j++) {
                if (s.charAt(j) == s.charAt(i) && (i - j <= 1 || isPalindrome[j + 1][i - 1])) {
                    // 加入i后新字符串整体是回文的，切分为0
                    if (j == 0) minCuts[i] = 0;
                    else minCuts[i] = Math.min(minCuts[i], minCuts[j - 1] + 1);
                    isPalindrome[j][i] = true;
                }
            }
        }
        
        return minCuts[n - 1];
    }
}
```

## Solution 2: DP optimized

- $TC:O(N^2)$
- $SC:O(N)$

```java
class Solution {
    public int minCut(String s) {
        if (s == null || s.length() <= 1) return 0;
        
        int n = s.length();
        int[] minCuts = new int[n];
        boolean[] isPalindrome = new boolean[n];
        
        for (int i = 0; i < n; i++) {
            minCuts[i] = i;
            for (int j = 0; j <= i; j++) {
                // 新一轮i的isPalindrome[j]取决于前一轮计算出的isPalindrome[j+1]
                if (s.charAt(j) == s.charAt(i) && (i - j <= 1 || isPalindrome[j + 1])) {
                    if (j == 0) minCuts[i] = 0;
                    else minCuts[i] = Math.min(minCuts[i], minCuts[j - 1] + 1);
                    isPalindrome[j] = true;
                } else {
                    // 注意在s(j,i)无法形成回文串时，还是需要更新对应的isPalindrome[j]
                    // 而不是直接跳过
                    isPalindrome[j] = false;
                }
            }
        }
        
        return minCuts[n - 1];
    }
}
```

## Solution 3: Expand from center

原理类似，不过是采用从中心扩展并判断回文串的方式，个人感觉相比前面的解法来说不是那么直观，但还是挺有意思的。

https://leetcode.com/problems/palindrome-partitioning-ii/discuss/42198/My-solution-does-not-need-a-table-for-palindrome-is-it-right-It-uses-only-O(n)-space.