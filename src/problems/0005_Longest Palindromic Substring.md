## 5. Longest Palindromic Substring (2020.12.26)

https://leetcode.com/problems/longest-palindromic-substring/

## Solution 1: DP

- $TC:O(N^2)$
- $SC:O(N^2)$
- Brute-force的解法是列举出所有的substring，然后遍历判断每个substring是不是palindromic，但是这种解法的时间复杂度会达到O(n^3)。
- 但是我们其实不需要每次都遍历整个substring来进行判断，因为存在状态转移关系s(i, j) = s(i) == s(j) and s(i+1, j-1)，即判断字符串s(i, j)是否是palindromic，只需要判断s(i)和s(j)是否相等，且字符串s(i+1, j-1)是否是palindromic，这样一来可以依赖之前的结果将判断的开销降低到O(1)。
- 所以，该解法也意味这要求得s(i, j)，必须先知道s(i+1, j-1)的结果。那么在遍历的时候需要采用i从最后开始递减，j从i处开始递增直至结束的方式。或者是先求出所有dist=1的情况，然后求dist=2，dist=3的情况，以此类推。
- Follow-up: 将空间压缩至O(N)

```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() <= 1) return s;
        
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int start = 0, len = 0;
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1]))
                    dp[i][j] = true;
                
                // 此处用start和max length来标记最长回文子串
                // 因为String的substring开销为O(N)
                if (dp[i][j] && j - i + 1 > len) {
                    start = i;
                    len = j - i + 1;
                }
            }
        }
        
        return s.substring(start, start + len);
    }
}
```

## Solution 2: Extend palindrome from center

- $TC:O(N^2)$
- $SC:O(1)$
- 该解法的思路是一个回文子串可以从选定的某个中心点i进行扩展。对于一个字符串s，这样的中心点共有2n-1个，其中n个是单中心点，n-1个是双中心点（e.g. `....aa....`）。当然也可以只对单中心点的情况进行扩展，但是需要先把和中心点字符duplicate的部分扩展开（分别对i,j单向扩展），再对边界同时进行扩展。

```java
class Solution {
    
    private int start = 0;
    private int end = 0;
    
    public String longestPalindrome(String s) {
        if (s == null || s.length() <= 1) return s;
        
        int n = s.length();
        
        for (int i = 0; i < n - 1; i++) {
            extendFromCenter(s, i, i);
            extendFromCenter(s, i ,i + 1);
        }
        
        return s.substring(start, end + 1);
    }
    
    private void extendFromCenter(String s, int i, int j) {
        while (i >= 0 && j < s.length() && s.charAt(i) == s.charAt(j)) {
            i--;
            j++;
        }
        
        // 需要注意这里的i，j要比真实需要的字符串边界扩展了1
        if (end - start + 1 < j - i - 1) {
            start = i + 1;
            end = j - 1;
        }
    }
}
```

