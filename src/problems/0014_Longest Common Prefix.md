## 14. Longest Common Prefix (2020.03.23)

https://leetcode.com/problems/longest-common-prefix/

## Solution 1: Horizontal scanning

- $TC:O(mn)$, `n为字符串个数, m为字符串长度(n个长为m的相同字符串的情况下)` (p.s. 第一个字符串是一个很长的字符串且剩余n-1个字符串都相同的情况下似乎更慢?)
- $SC:O(1)$
- $LCP(S_1,S_2,S_3,...,S_N) = LCP(LCP(LCP(S_1,S_2),S_3),...,S_N)$

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        
        String pre = strs[0];
        for (int i = 1; i < strs.length; i++) {
            while (strs[i].indexOf(pre) != 0) {
                pre = pre.substring(0, pre.length() - 1);
                if (pre.isEmpty()) return pre;
            }
        }
        
        return pre;
    }
}
```

## Solution 2: Vertical scanning

- $TC:O(mn)$, `n为字符串个数, m为字符串长度(n个长为m的相同字符串的情况下) `
- $SC:O(1)$
- Compare each character of strings

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        
        for (int i = 0; i < strs[0].length(); i++) {
            char c = strs[0].charAt(i);
            for (int j = 1; j < strs.length; j++) {
                if (i == strs[j].length() || c != strs[j].charAt(i)) {
                    return strs[0].substring(0, i);
                }
            }
        }
        
        return strs[0];
    }
}
```

## Solution 3: Divide and conquer

- $TC:O(mn)$, `n为字符串个数, m为字符串长度(n个长为m的相同字符串的情况下)` 
- $SC:O(1)$

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        
        return longestCommonPrefix(strs, 0, strs.length - 1);
    }
    
    private String longestCommonPrefix(String[] strs, int lp, int rp) {
        if (lp == rp) return strs[lp];
        
        int mid = (rp - lp) / 2 + lp;
        String left = longestCommonPrefix(strs, lp, mid);
        String right = longestCommonPrefix(strs, mid + 1, rp);
        return findLCP(left, right);
    }
    
    private String findLCP(String s1, String s2) {
        int min = Math.min(s1.length(), s2.length());
        for (int i = 0; i < min; i++) {
            if (s1.charAt(i) != s2.charAt(i)) return s1.substring(0, i);
        }
        
        return s1.substring(0, min);
    }
}
```

