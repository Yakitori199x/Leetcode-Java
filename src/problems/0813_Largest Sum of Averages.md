# 813. Largest Sum of Averages (2021.05.27)

https://leetcode.com/problems/largest-sum-of-averages/

## Solution 1: DP - Memorization

- $TC:O(k*n^2)$
- $SC:O(k*n)$
- 递归的思路，把A分成K份得到的子数组最大平均数之和，就等于当前子数组A[s...i]（作为被划分出的一份）的平均数加上以i+1为起点的A的剩余部分被分为K-1份得到的子数组最大平均数之和。

```java
class Solution {
    public double largestSumOfAverages(int[] A, int K) {
        int n = A.length;
        double[] preSum = new double[n + 1];
        for (int i = 0; i < n; i++) preSum[i + 1] = preSum[i] + A[i];
        double[][] memo = new double[n][K + 1];
        return largestSumOfAverages(A, K, 0, preSum, memo);
    }
    
    private double largestSumOfAverages(int[] A, int K, int s, double[] preSum, double[][] memo) {
        int n = A.length;
        if (memo[s][K] != 0) return memo[s][K];
        if (K == 1) {
            memo[s][K] = (preSum[n] - preSum[s]) / (n - s);
            return memo[s][K];
        }
        
        for (int i = s; i + K <= n; i++) {
            // A[s...i]的平均不需要前缀和也可以很快计算，这里前缀和主要是加速计算K == 1的情况
            memo[s][K] = Math.max(memo[s][K], (preSum[i + 1] - preSum[s]) / (i - s + 1) + largestSumOfAverages(A, K - 1, i + 1, preSum, memo));
        }
        return memo[s][K];
    }
}
```

## Solution 2: DP

- $TC:O(k*n^2)$
- $SC:O(k*n)$
- 从Solution1可以看出，我们可以把k=1的所有情况作为base case，一步一步递推到k=K的情况。
- 在该解法中，我们用dp[i][k]表示将nums[i...]（从i开始到nums末尾的这部分子数组）分成k份能够得到的最大平均数之和。
- 具体的递推过程见代码。

```java
class Solution {
    public double largestSumOfAverages(int[] nums, int K) {
        int n = nums.length;
        double[][] dp = new double[n][K + 1];
        double[] preSum = new double[n + 1];
        for (int i = 0; i < n; i++) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
        
        for (int k = 1; k <= K; k++) {
            for (int i = 0; i + k <= n; i++) {
                // base case
                // 对应的dp[i][1]等于后缀和
                if (k == 1) {
                    dp[i][1] = (preSum[n] - preSum[i]) / (n - i);
                    continue;
                }
                
                // 对于k > 1的情况，dp[i][k] = max(dp[j + 1][k - 1] + (preSum[j + 1] - preSum[i]) / (j - i + 1))
                for (int j = i; j + k <= n; j++) {
                    dp[i][k] = Math.max(dp[i][k], dp[j + 1][k - 1] + (preSum[j + 1] - preSum[i]) / (j - i + 1));
                }
            }
        }
        
        // 为什么可以直接返回dp[0][K]，而不是dp[0][K-1]或者更小的k？
        // 因为虽然说是最大可以分为K份（也就是也可以分不到K份），但其实能够把nums分成越多份，结果才能越好
        // 一个极端的例子就是nums中每个元素自己就是一份
        // 还有一种证明就是如果我能够将nums分成K-1份，那么必然能够对其中的某一份再进行切分，且切分后两部分的平均数之和必定大于切分前的那部分
        return dp[0][K];
    }
}
```

## Solution 3: DP - Optimized

- $TC:O(k*n^2)$
- $SC:O(n)$
- 从Solution2可以看出，dp[i][k]的值是从dp[* > i][k-1]求出来的，因此可以把空间进一步压缩。

```java
class Solution {
    public double largestSumOfAverages(int[] nums, int K) {
        int n = nums.length;
        double[] dp = new double[n];
        double[] preSum = new double[n + 1];
        for (int i = 0; i < n; i++) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
        
        for (int k = 1; k <= K; k++) {
            for (int i = 0; i + k <= n; i++) {
                if (k == 1) {
                    dp[i] = (preSum[n] - preSum[i]) / (n - i);
                    continue;
                }
                
                for (int j = i; j + k <= n; j++) {
                    dp[i] = Math.max(dp[i], dp[j + 1] + (preSum[j + 1] - preSum[i]) / (j - i + 1));
                }
            }
        }
        
        return dp[0];
    }
}
```

## Solution 4: DP - Back to front

- $TC:O(k*n^2)$
- $SC:O(k*n)$
- 从后往前切分，memo[e][k]表示从nums[0...e]切分成k份后得到的子数组最大平均数之和。

```java
class Solution {
    public double largestSumOfAverages(int[] nums, int k) {
        int n = nums.length;
        double[][] memo = new double[n][k + 1];
        double cur = 0;
        // 处理k=1的情况，可以直接用前缀和来计算
        for (int i = 0; i < n; i++) {
            cur += nums[i];
            memo[i][1] = cur / (i + 1);
        }
        return largestSumOfAverages(nums, k, n - 1, memo);
    }
    
    private double largestSumOfAverages(int[] nums, int k, int e, double[][] memo) {
        // 因为在递归之前已经处理过memo[*][1]的所有k = 1的base case
        // 所以在递归内就不需要额外处理
        if (memo[e][k] > 0) return memo[e][k];
        double cur = 0;
        for (int i = e; i >= k - 1; i--) {
            cur += nums[i];
            memo[e][k] = Math.max(memo[e][k], largestSumOfAverages(nums, k - 1, i - 1, memo) + cur / (e - i + 1));
        }
        return memo[e][k];
    }
}
```