## 344. Reverse String (2020.07.26)

https://leetcode.com/problems/reverse-string/

## Solution 1: Two pointers

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public void reverseString(char[] s) {
        while (s == null || s.length == 0) return;
        
        int i = 0, j = s.length - 1;
        while (i < j) {
            char tmp = s[i];
            s[i++] = s[j];
            s[j--] = tmp;
        }
    }
}
```

## Solution 2: Recursion

- $TC:O(n)$
- $SC:O(1)$ without recursion stack, otherwise $O(n)$

```java
class Solution {
    public void reverseString(char[] s) {
        if (s == null || s.length == 0) return;
        reverse(s, 0, s.length - 1);
    }
    
    private void reverse(char[] s, int i, int j) {
        if (i >= j) return;
        char tmp = s[i];
        s[i] = s[j];
        s[j] = tmp;
        reverse(s, i + 1, j - 1);
    }
}
```

