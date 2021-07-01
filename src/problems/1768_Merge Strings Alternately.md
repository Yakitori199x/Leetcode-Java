# 1768. Merge Strings Alternately (2021.03.31)

https://leetcode.com/problems/merge-strings-alternately/

## Solution 1: 2 Pointers

- $TC:O(m+n)$
- $SC:O(m+n)$

```java
class Solution {
    public String mergeAlternately(String word1, String word2) {
        int m = word1.length(), n = word2.length(), i = 0, j = 0;
        StringBuilder sb = new StringBuilder();
        while (i < m || j < n) {
            // 不需要专门去选出最小的长度
            // 直接判断各自的pointer是否超出各自的长度范围
            // 超出则不再加入sb
            if (i < m) sb.append(word1.charAt(i++));
            if (j < n) sb.append(word2.charAt(j++));
        }
        return sb.toString();
    }
}
```

## Solution 2: 1 Pointer

- $TC:O(m+n)$
- $SC:O(m+n)$

```java
class Solution {
    public String mergeAlternately(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < m || i < n; i++) {
            if (i < m) sb.append(word1.charAt(i));
            if (i < n) sb.append(word2.charAt(i));
        }
        return sb.toString();
    }
}
```