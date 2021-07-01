# 221. Maximal Square (2021.03.12)

https://leetcode.com/problems/maximal-square/

## Solution 1: Brute-force

- $TC:O(m^2*n^2)$
- $SC:O(1)$
- Brute-force的思想很直观，就是对每一个值为1的格子进行正方形的扩展（最外侧加入一行和一列）。
- 即初始状态为：格子本身构成一个边长为1的正方形，然后探索存在边长为2的正方形的可能性。
- 那么具体如何探索呢？只需要分别遍历因边长的增加，新加入到这个正方形的最外侧的一行和一列即可。
- 一直重复该过程并增加当前可能构成的正方形的边长，直到发现待探索的区域内存在值为0的格子。
- 如果发现有格子的值为0，那么说明无法构成相应边长的正方形，不需要继续扩展下去。
- 更新一下当前可构成的正方形的最大边长，然后进入对下一个值为1的格子的扩展。

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return 0;
        
        int m = matrix.length, n = matrix[0].length, maxSqLen = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    int curSqLen = 1;
                    boolean flag = true;
                    // 扩展之前先判断curSqLen的情况下，构成的正方形是否超出边界
                    while (i + curSqLen < m && j + curSqLen < n && flag) {
                        // 探索扩展出的行
                        for (int k = j; k <= j + curSqLen; k++) {
                            if (matrix[i + curSqLen][k] == '0') {
                                flag = false;
                                break;
                            }
                        }
                        
                        // 探索扩展出的列
                        for (int k = i; k <= i + curSqLen; k++) {
                            if (matrix[k][j + curSqLen] == '0') {
                                flag = false;
                                break;
                            }
                        }
                        
                        // 探索过程中没有发现0，说明可以构成相应的正方形，递增curSqLen
                        if (flag) curSqLen++;
                    }
                    
                    maxSqLen = Math.max(maxSqLen, curSqLen);
                }
            }
        }
        
        return maxSqLen * maxSqLen;
    }
}
```

## Solution 2: DP

- $TC:O(mn)$
- $SC:O(mn)$
- dp[i][j]表示以(i,j)为正方形的右下角顶点时可以获得的最大边长。
- 那么存在状态转移方程：dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])+1。
- 即以(i,j)为正方形的右下角顶点时可以获得的最大边长取决于其上方，左侧和对角线侧的点能够构成的正方形的最大边长。

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return 0;
        
        int m = matrix.length, n = matrix[0].length, maxSqLen = 0;

        // 将matrix[i][j]对应到dp[i+1][j+1]从而避免了处理边界情况
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    int x = i + 1, y = j + 1;
                    dp[x][y] = Math.min(Math.min(dp[x - 1][y - 1], dp[x - 1][y]), dp[x][y - 1]) + 1;
                    maxSqLen = Math.max(dp[i + 1][j + 1], maxSqLen);
                }
            }
        }
        
        return maxSqLen * maxSqLen;
    }
}
```

## Solution 3: DP - optimized

- $TC:O(mn)$
- $SC:O(n)$
- 对Solution2解法进行空间上的优化。

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return 0;
        
        int m = matrix.length, n = matrix[0].length, maxSqLen = 0, prev = 0;
        // 由于Solution2中，对dp的更新只用到了前一格以及上一行的dp，所以可以压缩成一维dp
        int[] dp = new int[n + 1];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 在更新当前dp时，先保存下更新前的值，这个值就对应更新下一个dp时对角线位置的顶点
                int temp = dp[j + 1];
                if (matrix[i][j] == '1') {
                    dp[j + 1] = Math.min(Math.min(dp[j + 1], dp[j]), prev) + 1;
                    maxSqLen = Math.max(maxSqLen, dp[j + 1]);
                } else {
                    // 在一维dp的情况下，需要对0格子对应的dp设置为0
                    dp[j + 1] = 0;
                }
                prev = temp;
            }
        }
        
        return maxSqLen * maxSqLen;
    }
}
```