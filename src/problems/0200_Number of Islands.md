## 200. Number of Islands (2020.10.29)

https://leetcode.com/problems/number-of-islands/

## Solution 1: DFS

- $TC:O(mn)$
- $SC:O(mn)$, 当所有位置均为1时，DFS会对grid进行从左到右，从右到左的蛇形遍历，递归栈将包含所有位置的元素

```java
class Solution {
    private int m;
    private int n;
    
    public int numIslands(char[][] grid) {
        m = grid.length;
        n = grid[0].length;
        int count = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    count++;
                }
            }
        }
        
        return count;
    }
    
    private void dfs(char[][] grid, int i, int j) {
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] != '1') return;
        
        // 记得将grid[i][j]标记为0，防止dfs无法停止，同时也避免了重复遍历的情况
        grid[i][j] = '0';
        dfs(grid, i, j + 1);
        dfs(grid, i, j - 1);
        dfs(grid, i + 1, j);
        dfs(grid, i - 1, j);
    }
}
```

## Solution 2: BFS

- $TC:O(mn)$
- $SC:O(对角线上元素个数)$

```java
class Solution {
    public int numIslands(char[][] grid) {
        int m = grid.length, n = grid[0].length;
        int count = 0;

        // 在需要遍历棋盘类数据结构中某个位置的周围四个位置时经常用到的偏置设定方法
        int[] offset = {0, 1, 0, -1, 0};
        Queue<List<Integer>> queue = new LinkedList<>();
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    grid[i][j] = '0';
                    count++;
                    queue.offer(Arrays.asList(i, j));
                    
                    while (!queue.isEmpty()) {
                        List<Integer> pair = queue.poll();
                        for (int k = 0; k < 4; k++) {
                            // offset的用法，可以遍历到上下左右的元素
                            int p = pair.get(0) + offset[k];
                            int q = pair.get(1) + offset[k + 1];
                            if (p >= 0 && p < m && q >= 0 && q < n && grid[p][q] == '1') {
                                // 对于BFS，在加入队列前就记得要设置为0
                                grid[p][q] = '0';
                                queue.offer(Arrays.asList(p, q));
                            }
                        }
                    }
                }
            }
        }
        
        return count;
    }
}
```

## Solution 3: Union-Find

TBA

https://leetcode.com/problems/number-of-islands/discuss/56354/1D-Union-Find-Java-solution-easily-generalized-to-other-problems