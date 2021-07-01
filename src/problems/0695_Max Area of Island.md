# 695. Max Area of Island (2021.02.20)

https://leetcode.com/problems/max-area-of-island/

## Solution 1: DFS ver1

- $TC:O(M*N)$
- $SC:O(max(M,N))$
- 本题基于沉岛思想，将遍历过的陆地格子设置为0，不需要维护visited。
- 不过这样会修改掉原数据，在实际应用中最好还是使用visited。

```java
class Solution {
    private int[] offsets = new int[]{-1, 0, 1, 0, -1};
    
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        
        int m = grid.length, n = grid[0].length, max = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    // 存在多个islands，对第一个遇到的island的结点DFS得到该island的面积，选取所有islands的最大值
                    max = Math.max(max, maxAreaOfIsland(grid, i, j));
                }
            }
        }
        
        return max;
    }
    
    private int maxAreaOfIsland(int[][] grid, int i, int j) {
        if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] == 0) return 0;
        
        grid[i][j] = 0;
        // 这一格已经确定是陆地，初始值为1
        int area = 1;
        for (int k = 0; k < offsets.length - 1; k++) {
            area += maxAreaOfIsland(grid, i + offsets[k], j + offsets[k + 1]);
        }
        return area;
    }
}
```

## Solution 2: DFS ver2

- $TC:O(M*N)$
- $SC:O(M*N)$
- 使用visited保存已经遍历过的格子。

```java
class Solution {
    private int[] offsets = new int[]{-1, 0, 1, 0, -1};
    
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        
        int m = grid.length, n = grid[0].length, max = 0;
        boolean[][] visited = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (!visited[i][j] && grid[i][j] == 1) {
                    max = Math.max(max, maxAreaOfIsland(grid, i, j, visited));
                }
            }
        }
        
        return max;
    }
    
    private int maxAreaOfIsland(int[][] grid, int i, int j, boolean[][] visited) {
        if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] == 0) return 0;
        if (visited[i][j]) return 0;
        
        visited[i][j] = true;
        int area = 1;
        for (int k = 0; k < offsets.length - 1; k++) {
            area += maxAreaOfIsland(grid, i + offsets[k], j + offsets[k + 1], visited);
        }
        return area;
    }
}
```