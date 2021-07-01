# 263. Ugly Number (2021.05.31)

https://leetcode.com/problems/ugly-number/

## Solution 1: Divide by 2, 3, 5

TC:O(logn)
SC:O(1)
如果n是丑数，那么它必定能够被分解为2，3和5的乘积。因此我们只需要反复判断n能否被i = 2/3/5整除（顺序不重要），如果可以则更新n = n/i继续判断。
最终判断n是否等于1即可。

```java
class Solution {
    public boolean isUgly(int n) {
        for (int i = 2; i < 6 && n > 0; i++) {
            while (n % i == 0) n = n / i;
        }
        return n == 1;
    }
}
```

## Solution 2: Divide by 2, 3, 5 ver2.

TC:O(logn)
SC:O(1)

```java
class Solution {
    public boolean isUgly(int n) {
        if (n == 0) return false;
        while (n % 2 == 0) n = n / 2;
        while (n % 3 == 0) n = n / 3;
        while (n % 5 == 0) n = n / 5;
        return n == 1;
    }
}
```