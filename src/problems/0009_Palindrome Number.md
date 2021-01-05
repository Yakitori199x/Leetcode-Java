## 9. Palindrome Number (2020.03.15)

https://leetcode.com/problems/palindrome-number/

## Solution 1

- $TC:O(log_{10}X)$
- $SC:O(1)$
- Reverse half of the number, avoid dealing with overflow, there may be some cases that rev = x when overflow happens

```java
class Solution {
    public boolean isPalindrome(int x) {
        if (x < 0 || (x != 0 && x % 10 == 0)) return false;
        
        int rev = 0;
        while (rev < x) {
            rev = 10 * rev + x % 10;
            x = x / 10;
        }
        
        return (x == rev || x == rev / 10); 
    }
}
```

