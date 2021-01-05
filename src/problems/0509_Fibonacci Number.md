## 509. Fibonacci Number (2020.11.25)

https://leetcode.com/problems/fibonacci-number/

## Solution 1: Iteration

- $TC:O(N)$
- $SC:O(1)$
- 按照斐波那契数列的定义进行迭代，相当于dp的最终优化版本。

```java
class Solution {
    public int fib(int N) {
        if (N <= 1) return N;
        
        int fn_1 = 1, fn_2 = 0;
        int fn = 0;
        for (int i = 2; i <= N; i++) {
            fn = fn_1 + fn_2;
            fn_2 = fn_1;
            fn_1 = fn;
        }
        
        return fn;
    }
}
```

## Solution 2: Square and Multiply algorithm

- $TC:O(logN)$
- $SC:O(1)$
- 本题只涉及2X2的矩阵运算，与N无关，所以矩阵运算的开销可以视作O(1)。
- https://leetcode.com/problems/fibonacci-number/discuss/223199/Java-O(2N)-greater-O(N)-greater-O(logN)

```java
class Solution {
    public int fib(int N) {
        if (N <= 1) return N;
        
        int[][] R = new int[][]{{1, 0}, {0, 1}};
        int[][] E = new int[][]{{1, 1}, {1, 0}};
        int exp = N - 1;
        
        // 斐波那契数列转化为矩阵运算的递推式可见链接
        // 此处用到了Square and Multiply algorithm
        // 例如要计算E^5，可以将指数看做二进制形式，即E^101 = E^(4+0+1)
        // 那么初始值为E，从右到左，先判断LSB是否为
        // 如果为1，则需要将R与以该位对应的系数为指数的E相乘
        // 同时，无论LSB是什么，都要将E的指数翻倍（即E*E）
        // 最后将exp右移一位后进入下一次计算
        while (exp > 0) {
            if ((exp & 1) != 0) R = multiply(E, R);
            E = multiply(E, E);
            exp >>= 1;
        }
        
        return R[0][0];
    }
    
    private int[][] multiply(int[][] A, int[][] B) {
        int N = A.length;
        int[][] ret = new int[N][N];
        
        // 由于本题只进行NxN矩阵的乘积，矩阵乘法的实现统一简化为N
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                for (int k = 0; k < N; k++) {
                    ret[i][j] += A[i][k] * B[k][j];
                }
            }
        }
        
        return ret;
    }
}
```

