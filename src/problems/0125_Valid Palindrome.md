## 125. Valid Palindrome (2020.07.05)

https://leetcode.com/problems/valid-palindrome/

## Solution 1: Two pointers

- $TC:O(N)$, N is the length of the string
- $SC:O(1)$
- Skip characters which are not alphabets and digits

```java
class Solution {
    public boolean isPalindrome(String s) {
        int l = 0, r = s.length() - 1;
        
        while (l <= r) {
            char lchar = s.charAt(l);
            char rchar = s.charAt(r);
            if (!Character.isLetterOrDigit(lchar)) l++;
            else if (!Character.isLetterOrDigit(rchar)) r--;
            else {
                if (Character.toLowerCase(lchar) != Character.toLowerCase(rchar)) return false;
                l++;
                r--;
            }
        }
        
        return true;
    }
}
```

