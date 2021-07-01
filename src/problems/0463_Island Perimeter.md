# 463. Island Perimeter (2021.02.20)

https://leetcode.com/problems/island-perimeter/

## Solution 1: Math

- $TC:O(M*N)$
- $SC:O(1)$
- 一个land可以有4条边，但是如果周围有相邻的其他land，那么连着的那条边则不能被计算进去。
- 即island的周长 = 4*land数量 - 2*邻接边数量。
- 因此，我们可以遍历grid中的所有格子，记录land数量的同时检查其右侧和下方是否有相邻的land（neighbor)。
- 这里只检查右侧和下方是因为我们最后会把neighbor*2，相当于说如果当前格子的左侧和上方有相邻的land，那么在之前的遍历中就会被计算进去。
- 如果grid中有多个island（连通分量），该方法也适用。

```java
class Solution {
    public int islandPerimeter(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        
        int land = 0, neighbor = 0, m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    land++;
                    if ((i + 1 < m) && (grid[i + 1][j] == 1)) neighbor++;
                    if ((j + 1 < n) && (grid[i][j + 1] == 1)) neighbor++;
                }
            }
        }
        
        return 4 * land - 2 * neighbor;
    }
}
```

## Solution 2: DFS

- $TC:O(M*N)$
- $SC:O(max(M,N))$
- 跟Solution1去计算land和neighbor的数量不同，该解法的思路是直接去计算land的border的数量。
- border就是指land与water连接的边或是land的边刚好是grid的边界的情况。
- 以下是递归的实现。循环实现的版本跟Solution1类似，直接遍历grid并check周围四格即可。

```java
class Solution {
    private int[] offsets = new int[]{-1, 0, 1, 0, -1};
    
    public int islandPerimeter(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    // 如果存在多个连通分量，那就不能直接return
                    return dfs(grid, i, j);
                }
            }
        }
        
        return 0;
    }
    
    private int dfs(int[][] grid, int i, int j) {
        if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length) return 1;
        if (grid[i][j] == 0) return 1;
        if (grid[i][j] == - 1) return 0;
        
        // 沉岛思想，即把遍历过的grid[i][j]设置为0（water）或是其他的一些flag
        // 可以防止DFS访问已经遍历过的结点
        grid[i][j] = -1;
        int count = 0;
        for (int k = 0; k < offsets.length - 1; k++) {
            count += dfs(grid, i + offsets[k], j + offsets[k + 1]);
        }
        
        return count;
    }
}
```
