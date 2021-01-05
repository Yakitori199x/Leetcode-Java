## 647. Palindromic Substrings (2020.12.27)

https://leetcode.com/problems/palindromic-substrings/

## Related Questions:
- 5. Longest Palindromic Substring

## Solution 1: DP

- $TC:O(N^2)$
- $SC:O(N^2)$
- 和Leetcode.5的解法一致，只不过这题求的是所有回文子串的数量。

```java
class Solution {
    public int countSubstrings(String s) {
        if (s == null) return 0;
        
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int res = 0;
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1])) {
                    dp[i][j] = true;
                }
                
                if (dp[i][j]) res += 1;
            }
        }
        
        return res;
    }
}
```

## Solution 2: Extend from center

- $TC:O(N^2)$
- $SC:O(1)$

```java
class Solution {
    
    private int res = 0;
    
    public int countSubstrings(String s) {
        if (s == null) return 0;
        int n = s.length();
        
        // i should be iterated until n - 1, because we want to find the amount
        for (int i = 0; i < n; i++) {
            extend(s, i, i); // check odd length substrings
            extend(s, i, i + 1); // check even length substrings
        }
        
        
        return res;
    }
    
    private void extend(String s, int i, int j) {
        while (i >= 0 && j < s.length() && s.charAt(i) == s.charAt(j)) {
            res++;
            i--;
            j++;
        }
    }
}
```

