# 994. Rotting Oranges (2021.03.08)

https://leetcode.com/problems/rotting-oranges/

## Solution 1: BFS

- $TC:O(m*n)$
- $SC:O(m*n)$

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    
    public int orangesRotting(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return -1;
        
        int m = grid.length, n = grid[0].length;
        int fresh = 0, minutes = 0;
        Queue<List<Integer>> queue = new LinkedList<>();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) fresh++;
                else if (grid[i][j] == 2) queue.offer(Arrays.asList(i, j));
            }
        }
        
        // 只有当有fresh且没有rotten的情况才直接返回-1，不过在后续BFS中也可以处理
        // 其他诸如没有fresh有rotten，既没有fresh也没rotten，都是返回0
        if (fresh == 0) return 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int k = 0; k < size; k++) {
                List<Integer> pos = queue.poll();
                int i = pos.get(0), j = pos.get(1);
                for (int[] dir : dirs) {
                    int x = i + dir[0], y = j + dir[1];
                    if (x < 0 || y < 0 || x >= m || y >= n || grid[x][y] != 1) continue;
                    queue.offer(Arrays.asList(x, y));
                    grid[x][y] = 2;
                    fresh--;
                }
            }
            minutes++;
        }
        
        return (fresh == 0) ? minutes - 1 : -1;
    }
}
```