## 70. Climbing Stairs (2020.05.19)

https://leetcode.com/problems/climbing-stairs/

## Solution 1: Pure Recursion

- $TC:O(2^N)$ `[TLE]`
- $SC:O(1)$
- Redundancy of calculations

```java
class Solution {
    public int climbStairs(int n) {
        if (n == 0) return 1;
        if (n == 1 || n == 2) return n;
        
        return climbStairs(n - 1) + climbStairs(n - 2);
    }
}
```

## Solution 2: Recursion with Memorization

- $TC:O(N)$
- $SC:O(N)$
- Save the imtermediate status to avoid redundancy, also prune the recursion tree

```java
class Solution {
    public int climbStairs(int n) {
        int[] mem = new int[n + 1];
        return climbStairs(n, mem);
    }
    
    private int climbStairs(int n, int[] mem) {
        if (n == 0) return 1;
        if (n == 1 || n == 2) return n;
        
        if (mem[n] > 0) return mem[n];
        mem[n] = climbStairs(n - 1, mem) + climbStairs(n - 2, mem);
        return mem[n];
    }
}
```

## Solution 3: Actually climbStairs(n) is n-th Fibonacci number

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public int climbStairs(int n) {
        if (n == 0) return 1;
        if (n == 1 || n == 2) return n;
        
        int oneStepBefore = 2, twoStepBefore = 1;
        int res = 0;
        for (int i = 3; i <= n; i++) {
            res = oneStepBefore + twoStepBefore;
            twoStepBefore = oneStepBefore;
            oneStepBefore = res;
        }
        
        return res;
    }
}
```

## Solution 4: DP

- $TC:O(N)$
- $SC:O(N)$
- dp[i] = dp[i - 1] + dp[i - 2]

```java
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
}
```

## Solution 5: Binets Method

- $TC:O(logN)$
- $SC:O(1)$
- n-th Fibonacci number F_n = Q^n, where Q = `[[1, 1], [1, 0]]`
- 矩阵快速幂 -- O(logn), n为指数
- From binary perspective, n can be viewed as the sum of power of two, so it only needs to calculate $Q^2, Q^4, Q^8 ...$
- move n towards left one bit each time (the same as divide by 2)
- check if the least significant bit is 1, if it is, multiply with former result

```java
class Solution {
    public int climbStairs(int n) {
        if (n == 0) return 1;
        if (n == 1 || n == 2) return n;
        
        int[][] q = {{1, 1}, {1, 0}};
        int[][] res = pow(q, n);
        return res[0][0];
    }
    
    private int[][] pow(int[][] q, int n) {
        int[][] res = {{1, 0}, {0, 1}};
        while (n > 0) {
            if ((n & 1) == 1) res = multiply(res, q);
            n = n >> 1;
            q = multiply(q, q);
        }
        
        return res;
    }
    
    private int[][] multiply(int[][] a, int[][] b) {
        int[][] c = new int[2][2];
        
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j]; 
            }
        }
        
        return c;
    }
}
```

