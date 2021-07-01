# 688. Knight Probability in Chessboard (2021.03.01)

https://leetcode.com/problems/knight-probability-in-chessboard/

## Solution 1: DFS, brute-force

- $TC:O(8^K)$
- $SC:O(K)$ 
- 最朴素的思路，即从起点(r,c)出发，向八个不同的方向进行DFS，每个方向的概率是0.125。
- 如果新的位置已经超出了边界，则说明这条path不能留在棋盘内，直接返回0。
- 如果剩余步数K达到0，说明已经不能继续走下去了，且由上面的判断可知当前点还在棋盘内，则返回1。
- 最后返回八个方向上的概率之和即可。缺点就是会TLE，需要进一步优化。

```java
class Solution {
    private int[][] dirs = new int[][]{{2, 1}, {2, -1}, {-2, 1}, {-2, -1}, {1, 2}, {-1, 2}, {1, -2}, {-1, -2}};
    
    public double knightProbability(int N, int K, int r, int c) {
        if (r < 0 || r >= N || c < 0 || c >= N) return 0;
        if (K == 0) return 1;
        
        double res = 0;
        for (int[] dir : dirs) {
            res += 0.125 * knightProbability(N, K - 1, r + dir[0], c + dir[1]);
        }
        
        return res;
    }
}
```

## Solution 2: DFS, memorization

- $TC:O(K*N^2)$
- $SC:O(K*N^2)$
- 用一个memo来记录之前遍历过的结果，避免一些重复的计算（剪枝）。
- memo[r][c][K]表示从(r,c)出发移动K步后还能够停留在棋盘内的概率。
- 把memo的所有值初始化为负值应该可以进一步提升效率，因为可能存在某些位置确实无法在移动K步后停留在棋盘内。
- 对于这些位置，计算出来的memo值为0。如果memo的初始值也是0，那就无法判断这些位置是否是已经计算过的，只能重新计算。

```java
class Solution {
    private int[][] dirs = new int[][]{{2, 1}, {2, -1}, {-2, 1}, {-2, -1}, {1, 2}, {-1, 2}, {1, -2}, {-1, -2}};
    
    public double knightProbability(int N, int K, int r, int c) {
        double[][][] memo = new double[N][N][K + 1];
        return knightProbability(N, K, r, c, memo);
    }
    
    private double knightProbability(int N, int K, int r, int c, double[][][] memo) {
        if (r < 0 || r >= N || c < 0 || c >= N) return 0;
        if (K == 0) return 1;
        if (memo[r][c][K] != 0) return memo[r][c][K];
        
        double res = 0;
        for (int[] dir : dirs) {
            res += 0.125 * knightProbability(N, K - 1, r + dir[0], c + dir[1], memo);
        }
        memo[r][c][K] = res;
        return res;
    }
}
```

## Solution 3: DP

- $TC:O(K*N^2)$
- $SC:O(K*N^2)$
- 我们可以设定dp[i][j][k]表示从起点(r,c)出发移动k步后到达点(i,j)的概率。
- 那么初始状态就是dp[r][c][0]=1，因为从(r,c)出发移动0步到达(r,c)的概率肯定是1。
- 于是我们可以推导出状态转移方程：dp[i][j][k] = sum(0.125*dp[i+dir1][j+dir2][k-1])（dir1和dir2表示一个方向，且i+dir1和j+dir2要在棋盘内才有效）。
- 通过上式，我们可以看出k下的dp完全是通过k-1下的dp计算出来的。
- 因此我们只需要维护一个原始的dp，和一个新计算的temp，计算后将temp赋值给dp继续往下计算即可。

```java
class Solution {
    private int[][] dirs = new int[][]{{2, 1}, {2, -1}, {-2, 1}, {-2, -1}, {1, 2}, {-1, 2}, {1, -2}, {-1, -2}};
    
    public double knightProbability(int N, int K, int r, int c) {
        double[][] dp = new double[N][N];
        // base case
        dp[r][c] = 1;
        
        for (int k = 1; k <= K; k++) {
            double[][] temp = new double[N][N];
            for (int i = 0; i < N; i++) {
                for (int j = 0; j < N; j++) {
                    for (int[] dir : dirs) {
                        int x = i + dir[0], y = j + dir[1];
                        // 超出边界，概率为0，直接进入下一个循环
                        if (x < 0 || x >= N || y < 0 || y >= N) continue;
                        // (x,y)再移动一步就是(i,j)
                        // 所以从(r,c)移动k步到达(i,j)的概率
                        // 就等于从(r,c)移动k-1步到达(x,y)的概率*往相应方向移动的概率(0.125)
                        temp[i][j] += 0.125 * dp[x][y];
                    }
                }
            }
            dp = temp;
        }
        
        // 最后要将移动k步后所有棋盘内可能到达的点的概率加起来
        double res = 0;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                res += dp[i][j];
            }
        }
        return res;
    }
}
```