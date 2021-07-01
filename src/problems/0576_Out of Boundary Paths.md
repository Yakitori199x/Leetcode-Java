# 576. Out of Boundary Paths (2021.03.01)

https://leetcode.com/problems/out-of-boundary-paths/

## Related Questions:
- 688. Knight Probability in Chessboard

## Solution 1: DFS, brute-force

- $TC:O(4^N)$
- $SC:O(N)$ 
- Naive的思路，从起点(i,j)出发向四个可移动的方向进行DFS。
- 如果超出了边界，说明是一种可行的解，返回1。否则，若剩余可移动步数N达到0，则说明这条Path不可能通向边界外，返回0。
- 由于本题的最终答案可能很大导致溢出，所以需要mod题目给定的模数。

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    private int MOD = 1000000000 + 7;
    
    public int findPaths(int m, int n, int N, int i, int j) {
        if (i < 0 || i >= m || j < 0 || j >= n) return 1;
        if (N == 0) return 0;
        
        int res = 0;
        for (int[] dir : dirs) {
            // 递归返回值取一次模，和当前值相加后也有可能溢出，再取一次模
            res = (res + findPaths(m, n, N - 1, i + dir[0], j + dir[1]) % MOD) % MOD;
        }
        
        return res;
    }
}
```

## Solution 2: DFS, memorization

- $TC:O(N*m*n)$
- $SC:O(N*m*n)$
- 同样地，可以使用memo来进行记忆化递归，从而对时间复杂度进行优化。
- 这里的memo[i][j][N]表示从点(i,j)出发移动N步可以走出边界的Path数。

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    private int MOD = 1000000000 + 7;
    
    public int findPaths(int m, int n, int N, int i, int j) {
        int[][][] memo = new int[m][n][N + 1];
        // 本题需要将初始值设定为-1，否则还是会超时
        // 因为有些位置就是不能在一定步数内走出边界，这时候memo[i][j][N]=0
        // 如果用0来判断是否可以直接返回memo值，那就会导致这部分的重复计算
        for (int a = 0; a < m; a++) {
            for (int b = 0; b < n; b++) {
                for (int c = 1; c <= N; c++) {
                    memo[a][b][c] = -1;
                }
            }
        }
        return findPaths(m, n, N, i, j, memo);
    }
    
    private int findPaths(int m, int n, int N, int i, int j, int[][][] memo) {
        if (i < 0 || i >= m || j < 0 || j >= n) return 1;
        if (N == 0) return 0;
        if (memo[i][j][N] != -1) return memo[i][j][N];
        
        int res = 0;
        for (int[] dir : dirs) {
            res = (res + findPaths(m, n, N - 1, i + dir[0], j + dir[1], memo) % MOD) % MOD;
        }
        
        memo[i][j][N] = res;
        return res;
    }
}
```

## Solution 3: DP

- $TC:O(N*m*n)$
- $SC:O(N*m*n)$
- 我们可以设定dp[i][j][k]表示从起点(r,c)出发移动k步后能够到达点(i,j)存在的Path数。
- 那么初始状态就是dp[r][c][0]=1，因为从(r,c)出发移动0步到达(r,c)的Path只有1条。
- 所以，状态转移方程可以是：若从点(i,j)移动后得到的(x,y)超出边界，则将dp[i][j][k-1]累加到最终答案res上。
- 否则，说明从起点开始移动k步到达(x,y)的Path数中包含了移动k-1步到(i,j)的Path数，即dp[x][y][k] += dp[i][j][k-1]。
- 通过上式，我们可以看出k下的dp完全是通过k-1下的dp计算出来的。
- 因此我们只需要维护一个原始的dp，和一个新计算的temp，计算后将temp赋值给dp继续往下计算即可。

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    private int MOD = 1000000000 + 7;
    
    public int findPaths(int m, int n, int N, int i, int j) {
        int[][] dp = new int[m][n];
        dp[i][j] = 1;
        int res = 0;
        
        for (int k = 1; k <= N; k++) {
            int[][] temp = new int[m][n];
            for (int a = 0; a < m; a++) {
                for (int b = 0; b < n; b++) {
                    for (int[] dir : dirs) {
                        // 从(a,b)移动到(x,y)
                        // 若(x,y)出界，则说明对应Path是我们需要的
                        // 若(x,y)仍在范围内，说明k步到达(x,y)的Path包含了k-1步到达(a,b)的Path
                        int x = a + dir[0], y = b + dir[1];
                        if (x < 0 || x >= m || y < 0 || y >= n) {
                            res = (res + dp[a][b]) % MOD;
                        } else {
                            temp[x][y] = (temp[x][y] + dp[a][b]) % MOD;
                        }
                    }
                }
            }
            dp = temp;
        }
        
        return res;
    }
}
```