## 69. Sqrt(x) (2020.05.20)

https://leetcode.com/problems/sqrtx/

## Solution 1: Brute force

- $TC:O(N)$
- $SC:O(1)$
- Search all the possible integers leq to x

```java
class Solution {
    public int mySqrt(int x) {
        for (int i = 1; i <= x; i++) {
            if (i <= x / i && (i + 1) > x / (i + 1))
                return i;
        }
        
        return 0;
    }
}
```

## Solution 2: Binary Search

- $TC:O(logN)$
- $SC:O(1)$
- Trick: use [mid <= x / mid] instead of [mid * mid <= x] to avoid overflow

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) return 0;
        
        int l = 1, r = x;
        int res = 0;
        
        while (l <= r) {
            int mid = (r - l) / 2 + l;
            if (mid <= x / mid) l = mid + 1;
            else r = mid - 1;
        }
        
        return r;
    }
}
```

## Solution 3: Newton's method

- $TC:TBD$
- $SC:O(1)$
- Trick: [(res + x / res)] might cause overflow, so we need to set res as long, `e.g. (res + x / res) = (MAX_INTEGER + 1) -> overflow`

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) return 0;
        
        long res = x;
        
        while (res > x / res) {
            res = (res + x / res) / 2;
        }
        
        return (int) res;
    }
}
```

