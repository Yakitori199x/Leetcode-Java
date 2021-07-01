# 1140. Stone Game II (2021.05.18)

https://leetcode.com/problems/stone-game-ii/

## Solution 1: DP - Memorization

- $TC:O(n^3)$
- $SC:O(n^2)$
- 虽然memo的大小为n*n，但是每次递归都需要探索1到2 * M的可能取法，所以总体的时间开销会达到O(n^3)，这一点在Solution2的bottom-up DP可以很明显地看出来。
- 该解法的核心思想为：当前玩家可以从i开始选取1到2 * m个piles，而另一位玩家就需要在当前玩家的选取结果基础上进行操作。比如当前玩家选择了前k个piles，那么下一位玩家就是从i+k开始选取，且对应的M为max(k, m)。
- 因此，我们可以定义状态memo[i][j]为当前玩家在piles[i:]且M=j的情况下可以获得的最多stones。
- 那么根据上述结论可知，存在memo[i][j] = max(suffix[i] - memo[i + k][max(k, j)])，suffix[i]为piles[i:]的所有stones数量，且1 <= k <= 2 * j。

```java
class Solution {
    public int stoneGameII(int[] piles) {
        int n = piles.length;
        int[] suffixSum = Arrays.copyOf(piles, n);
        int[][] memo = new int[n][n];
        
        // 计算piles的后缀和，表示从i开始的所有stones
        for (int i = n - 2; i >= 0; i--) suffixSum[i] = suffixSum[i] + suffixSum[i + 1];
        return stoneGameII(piles, 0, 1, memo, suffixSum);
    }
    
    private int stoneGameII(int[] piles, int start, int M, int[][] memo, int[] suffixSum) {
        // 如果当前可以选取的piles已经超过了最后一个pile，那么必然是全部选取对当前玩家来说是最优的选择
        if (start + 2 * M >= piles.length) return suffixSum[start];
        if (memo[start][M] != 0) return memo[start][M];
        
        int res = 0;
        for (int i = 1; i <= 2 * M; i++) {
            // stoneGameII(piles, start + i, Math.max(M, i))为另一位玩家接下来在piles[start + i]且M为Math.max(M, i)的情况下可以获得的最多stones
            // 那么当前玩家能获得的最多stones就是从start开始的所有stones减去另一位玩家对应获得的stones的最大值
            res = Math.max(res, suffixSum[start] - stoneGameII(piles, start + i, Math.max(M, i), memo, suffixSum));
        }
        memo[start][M] = res;
        return res;
    }
}
```

## Solution 2: DP

- $TC:O(n^3)$
- $SC:O(n^2)$

```java
class Solution {
    public int stoneGameII(int[] piles) {
        int n = piles.length;
        int[] suffixSum = Arrays.copyOf(piles, n);
        int[][] dp = new int[n + 1][n + 1];
        for (int i = n - 2; i >= 0; i--) suffixSum[i] = suffixSum[i] + suffixSum[i + 1];
        for (int i = 0; i < n; i++) dp[i][n] = suffixSum[i];
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = 1; j < n; j++) {
                for (int x = 1; x <= 2 * j && i + x <= n; x++) {
                    dp[i][j] = Math.max(dp[i][j], suffixSum[i] - dp[i + x][Math.max(x, j)]);
                }
            }
        }
        
        return dp[0][1];
    }
}
```