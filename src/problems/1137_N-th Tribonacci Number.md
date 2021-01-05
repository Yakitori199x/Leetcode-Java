## 1137. N-th Tribonacci Number (2021.01.01)

https://leetcode.com/problems/n-th-tribonacci-number/

## Related Questions:
- 509. Fibonacci Number

## Solution 1: Iterative (Optimized DP)

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public int tribonacci(int n) {
        if (n <= 1) return n;
        
        int fn = 0, fn_1 = 1, fn_2 = 1, fn_3 = 0;
        for (int i = 3; i <= n; i++) {
            fn_3 = fn + fn_1 + fn_2;
            fn = fn_1;
            fn_1 = fn_2;
            fn_2 = fn_3;
        }
        
        // 返回fn+2，可以不用额外对n=2的情况进行判断
        return fn_2;
    }
}
```

## Solution 2: Iterative (Modular way)

- $TC:O(N)$
- $SC:O(1)$
- 一种比较巧妙的迭代方法

```java
class Solution {
    public int tribonacci(int n) {
        int[] dp = {0, 1, 1};
        for (int i = 3; i <= n; i++) {
            dp[i % 3] = dp[0] + dp[1] + dp[2];
        }
        
        return dp[n % 3];
    }
}
```

## Solution 3: Matrix (见Related Question)

TBA