# 1091. Shortest Path in Binary Matrix (2021.03.09)

https://leetcode.com/problems/shortest-path-in-binary-matrix/

## Solution 1: BFS

- $TC:O(n^2)$
- $SC:O(n)$
- 如果graph中的每条边对应的权重都相等（比如这里的grid），那么最短路径问题可以直接通过BFS解决。
- 本题就是从(0,0)出发，往八个方向（上下左右+对角线）进行BFS层次遍历，直到找到重点(n-1,n-1)。
- 遍历过的level就是所求的path的长度。

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1},
                                       {1, -1}, {1, 1}, {-1, 1}, {-1, -1}};
    
    public int shortestPathBinaryMatrix(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return -1;
        
        int n = grid.length;
        // 如果起点和终点的值为1，那么必不存在clear path
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) return -1;
        
        Queue<List<Integer>> queue = new LinkedList<>();
        boolean[][] visited = new boolean[n][n];
        queue.offer(Arrays.asList(0, 0));
        visited[0][0] = true;
        int res = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            res++;
            for (int k = 0; k < size; k++) {
                List<Integer> pos = queue.poll();
                int i = pos.get(0), j = pos.get(1);
                if (i == n - 1 && j == n - 1) return res;
                for (int[] dir : dirs) {
                    int x = i + dir[0], y = j + dir[1];
                    if (x < 0 || y < 0 || x >= n || y >= n || visited[x][y] || grid[x][y] == 1) continue;
                    queue.offer(Arrays.asList(x, y));
                    visited[x][y] = true;
                }
            }
        }
        
        return -1;
    }
}
```