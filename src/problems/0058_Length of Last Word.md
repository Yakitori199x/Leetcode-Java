## 58. Length of Last Word (2020.05.14)

https://leetcode.com/problems/length-of-last-word/

## Solution 1

- $TC:O(n)$
- $SC:O(1)$
- Iterate from the end of the string, skip all the space until find the first non-space character
- Then keep moving backwards and count the number of non-space characters until meet the next space or iterate over the string

```java
class Solution {
    public int lengthOfLastWord(String s) {
        if (s == null) return 0;
        
        int res = 0;
        int tail = s.length() - 1;
        while (tail >= 0 && s.charAt(tail) == ' ') tail--;
        while (tail >= 0 && s.charAt(tail) != ' ') {
            res++;
            tail--;
        }
        
        return res;
    }
}
```

## Solution 2: Use Java built-in methods

- $TC:O(n)$
- $SC:O(1)$
- The TC of trim() is O(n), and that of lastIndexOf() is also O(n) when the passed parameter is single character, we can also use split() method of String

```java
class Solution {
    public int lengthOfLastWord(String s) {
        if (s == null) return 0;
        return s.trim().length() - s.trim().lastIndexOf(' ') - 1;
    }
}
```

