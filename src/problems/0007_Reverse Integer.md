## 7. Reverse Integer (2020.03.11)

https://leetcode.com/problems/reverse-integer/

## Solution 1

- $TC:O(log_{10}X)$
- $SC:O(1)$
- If long is allowed, we can compare it with maximum and minimum of int to check overflow.

```java
class Solution {
    public int reverse(int x) {
        long res = 0;
        
        while (x != 0) {
            int remainder = x % 10;
            res = 10 * res + remainder;
            if (res > Integer.MAX_VALUE || res < Integer.MIN_VALUE) return 0;
            x = x / 10;
        }
        
        return (int)res;
    }
}
```

## Solution 2

- $TC:O(log_{10}X)$
- $SC:O(1)$
- If long is not allowed, check whether the new result divided by 10 will equal to previous result.

```java
class Solution {
    public int reverse(int x) {
        int res = 0;
        int prevRes = 0;
        
        while (x != 0) {
            int remainder = x % 10;
            res = 10 * prevRes + remainder;
            if (res / 10 != prevRes ) return 0;
            prevRes = res;
            x = x / 10;
        }
        
        return res;
    }
}
```

