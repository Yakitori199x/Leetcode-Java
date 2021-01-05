## 28. Implement strStr() (2020.11.08)

https://leetcode.com/problems/implement-strstr/

## Solution 1

- $TC:O(m*n)$
- $SC:O(1)$

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (haystack == null || needle == null) return -1;
        if (needle.length() == 0) return 0;
        
        int m = haystack.length(), n = needle.length();
        for (int i = 0; i <= m - n; i++) {
            int j = 0;
            for (; j < n; j++) {
                if (haystack.charAt(i + j) != needle.charAt(j)) break;
            }
            
            if (j == n) return i;
        }
        
        return -1;
    }
}
```

## Solution 2: KMP

TBA

https://leetcode.com/problems/implement-strstr/discuss/12956/C%252B%252B-Brute-Force-and-KMP
