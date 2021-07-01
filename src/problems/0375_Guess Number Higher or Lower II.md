# 375. Guess Number Higher or Lower II (2021.05.14)

https://leetcode.com/problems/guess-number-higher-or-lower-ii/

## Solution 1: DP (Memorization)

- $TC:O(n^2)$
- $SC:O(n^2)$
- 本题的目的就是求出能保证猜到目标数所需付出的最小开销。（比如你把所有数都猜一遍也能确保猜到，但其对应的是最大的开销）
- 由于猜错一个数后，需要付出相当于这个数的开销，因此不能简单地把取得最小开销的方法想成每次二分搜索取中点。（如果每次付出的开销均相等，或许二分搜索就是最优？）
- 因此，对于待定范围[i,j]，我们还是需要对i到j之间的每一个数k进行猜测，并且获取它们中的最小值。
- 其次，若某次猜测的数为k，因为我们无法保证目标数一定在k的左半部分还是右半部分，要保证能找到，我们就需要对两种情况的都进行求解，对应开销为k+max([i,k-1],[k+1,j])。（比如[i,k-1]对应开销比[k+1,j]来得小，但可能目标数就在[k+1,j]中，如果取[i,k-1]的结果就无法保证一定在这个开销的情况下能够找到）

```java
class Solution {
    public int getMoneyAmount(int n) {
        int[][] memo = new int[n + 1][n + 1];
        return getMoneyAmount(1, n, memo);
    }
    
    private int getMoneyAmount(int s, int e, int[][] memo) {
        if (s >= e) return 0;
        if (memo[s][e] != 0) return memo[s][e];
        int cost = Integer.MAX_VALUE;
        for (int i = s; i <= e; i++) {
            cost = Math.min(cost, i + Math.max(getMoneyAmount(s, i - 1, memo), getMoneyAmount(i + 1, e, memo)));
        }
        memo[s][e] = cost;
        return cost;
    }
}
```

## Solution 2: DP (Bottom-Up)

- $TC:O(n^2)$
- $SC:O(n^2)$
- 我们可以定义dp[i][j]为在[i,j]范围内进行猜测所需的最小开销。
- 那么状态转移方程即为：dp[i][j] = min(k + max(dp[i][k - 1], dp[k + 1][j]))，k为[i,j]内取得的某个值。

```java
class Solution {
    public int getMoneyAmount(int n) {
        // 把dp状态数组声明为(n + 2)*(n + 2)是为了便于处理corner cases
        // 比如j=n且k=j=n的情况下，dp[k + 1][j]就相当于dp[n + 1][n]
        // 抑或是i=1且k=i=1的情况下，dp[i][k - 1]就相当于dp[1][0]
        int[][] dp = new int[n + 2][n + 2];

        // 从状态转移方程中可以看出，要推导出dp[i][j]，就要先求出dp[i][k - 1]和dp[k + 1][j]
        // dp[i][k - 1]位于dp[i][j]的左侧，dp[k + 1][j]位于dp[i][j]的下方
        // 因此，我们的递推顺序应当是从左到右，从下到上的，最终推导求出dp[1][n]即为我们的答案
        for (int i = n; i >= 1; i--) {
            for (int j = i; j <= n; j++) {
                if (i == j) {
                    dp[i][j] = 0;
                } else {
                    int temp = Integer.MAX_VALUE;
                    for (int k = i; k <= j; k++) {
                        temp = Math.min(temp, k + Math.max(dp[i][k - 1], dp[k + 1][j]));
                    }
                    dp[i][j] = temp;
                }
            }
        }
        return dp[1][n];
    }
}
```