## 62. Unique Paths (2020.11.16)

https://leetcode.com/problems/unique-paths/

## Solution 1: DP - original

- $TC:O(m*n)$
- $SC:O(m*n)$
- 设dp[i][j]为移动到第(i,j)格时存在的unique path的数量。因为只能向右或是向下移动，所以有dp[i][j] = dp[i-1][j] + dp[i][j-1]，即当前格子的unique path等于上方格子的unique path加上左侧格子的unique path，其中上边第一行和左侧第一列的初始值均为1。

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        
        for (int i = 0; i < m; i++) dp[i][0] = 1;
        for (int j = 0; j < n; j++) dp[0][j] = 1;
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        
        return dp[m - 1][n - 1];
    }
}
```

## Solution 2: DP - optimize space

- $TC:O(m*n)$
- $SC:O(n)$
- 不难发现，要计算出dp[i][j]，只需要上行同列的dp和左侧最新计算出的dp，因此我们可以将空间的消耗压缩到两个长度为row的数组pre和cur。状态转移方程变为cur[j] = cur[j-1] + pre[j]，且在计算完一行新的值后对pre和cur进行交换。

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[] pre = new int[n];
        int[] cur = new int[n];
        
        for (int i = 0; i < n; i++) pre[i] = 1;
        cur[0] = 1;
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                cur[j] = cur[j - 1] + pre[j];
            }
            
            int[] tmp = pre;
            pre = cur;
            cur = tmp;
        }
        
        return pre[n - 1];
    }
}
```

## Solution 3: DP - further optimize

- $TC:O(m*n)$
- $SC:O(n)$
- 根据solution2的结论cur[j] = cur[j-1] + pre[j]，我们甚至可以只使用一个1d数组来进行计算，只需要将更新后的值覆盖掉同位置的旧值即可，即dp[j] = dp[j-1] + dp[j]。

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[] dp = new int[n];
        
        for (int i = 0; i < n; i++) dp[i] = 1;
        
        // 此处的i只作为需要循环几行的指示，并不直接参与到数组迭代
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[j] = dp[j - 1] + dp[j];
            }
        }
        
        return dp[n - 1];
    }
}
```

## Solution 4: Math (Permutation & Combination)

- $TC:O(max(m,n))$
- $SC:O(1)$
- 我们用R表示向右移动，用D表示向下移动，那么在一个4X3的图中，path必然是5个由R和D组成的序列（注意，4X3的图中最多向下2次，向右3次），比如RRDDR。所以，所有的unique path就相当于求解在5个格子中选出两个格子为D（或是3个格子为R）总共有几种可能，即$C_{5}^{2}$。
- 以此类推到mXn的情况，则解为$C_{m+n-2}^{m-1}$。具体的推导可参考：https://betterexplained.com/articles/navigate-a-grid-using-combinations-and-permutations/

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int N = m + n - 2, k = m - 1;
        long res = 1;
        
        for (int i = 1; i <= k; i++) {
            // 注意先乘后除，否则会丢失小数部分的值，例：m=7,n=3
            res = res * (N - k + i) / i;
        }
        
        return (int) res;
    }
}
```

## Solution 5: Recursion

- $TC:O(2^n)$
- $SC:O(m+n)$
- pure的递归包含了太多重复计算，时间复杂度会达到指数级别。

```java
class Solution {
    public int uniquePaths(int m, int n) {
        return uniquePathsHelper(m - 1, n - 1);
    }
    
    public int uniquePathsHelper(int m, int n) {
        if (m < 0 || n < 0) return 0;
        if (m == 0 || n == 0) return 1;
        
        return uniquePaths(m - 1, n) + uniquePaths(m, n - 1);
    }
}
```

## Solution 6: Memorization Recursion

- $TC:O(m*n)$
- $SC:O(m*n)$
- 引入memory数组来存储递归过程中已经计算出的值，以避免重复计算。

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] mem = new int[m][n];
        return uniquePaths(m - 1, n - 1, mem);
    }
    
    private int uniquePaths(int m, int n, int[][] mem) {
        if (m < 0 || n < 0) return 0;
        if (m == 0 || n == 0) return 1;
        if (mem[m][n] != 0) return mem[m][n];
        
        mem[m][n] = uniquePaths(m - 1, n, mem) + uniquePaths(m, n - 1, mem);
        return mem[m][n];
    }
}
```

