# 59. Spiral Matrix II (2021.06.29)

https://leetcode.com/problems/spiral-matrix-ii/

## Similar Questions:
- 54. Spiral Matrix

## Solution 1: Simulation
- $TC:O(n*n)$
- $SC:O(1)$
- 和LC54的思路其实是一致的，只不过LC54是从矩阵中读，本题则是往矩阵中写。

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] res = new int[n][n];
        int sRow = 0, eRow = n - 1, sCol = 0, eCol = n - 1, cnt = 1;
        while (cnt <= n * n) {
            for (int j = sCol; j <= eCol && cnt <= n * n; j++) {
                res[sRow][j] = cnt++;
            }
            
            for (int i = sRow + 1; i <= eRow - 1 && cnt <= n * n; i++) {
                res[i][eCol] = cnt++;
            }
            
            for (int j = eCol; j >= sCol && cnt <= n * n; j--) {
                res[eRow][j] = cnt++;
            }
            
            for (int i = eRow - 1; i >= sRow + 1 && cnt <= n * n; i--) {
                res[i][sCol] = cnt++;
            }
            
            sRow++; eRow--; sCol++; eCol--;
        }
        return res;
    }
}
```

## Solution 2: Simulation - Direction

- $TC:O(n*n)$
- $SC:O(1)$

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] res = new int[n][n];
        int[][] dirs = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1,0}};
        int r = 0, c = 0, cnt = 1, d = 0;
        while (cnt <= n * n) {
            res[r][c] = cnt++;
            // 根据当前的前进方向计算出下一个row和col
            // 如果nr和nc超出了矩阵的边界或是其对应的格子已经被填充，说明需要改变前进方向
            int nr = r + dirs[d][0], nc = c + dirs[d][1];
            if (nr < 0 || nr >= n || nc < 0 || nc >= n || res[nr][nc] != 0) d = (d + 1) % 4;
            r = r + dirs[d][0];
            c = c + dirs[d][1];
        }
        return res;
    }
}
```