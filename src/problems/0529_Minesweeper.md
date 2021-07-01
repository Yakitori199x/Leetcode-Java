# 529. Minesweeper (2021.03.08)

https://leetcode.com/problems/minesweeper/

## Solution 1: BFS

- $TC:O(m*n)$
- $SC:O(m+n)$，当click的点刚好在中心且board上几乎都是E的情况下，BFS会一圈一圈向外扩展

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}, {1, 1}, {1, -1}, {-1, 1}, {-1,-1}};
    
    public char[][] updateBoard(char[][] board, int[] click) {
        if (board == null || board.length == 0 || board[0].length == 0) return board;
        
        int m = board.length, n = board[0].length;
        Queue<List<Integer>> queue = new LinkedList<>();
        queue.offer(Arrays.asList(click[0], click[1]));
        while (!queue.isEmpty()) {
            List<Integer> pos = queue.poll();
            int i = pos.get(0), j = pos.get(1);
            if (board[i][j] == 'M') {
                board[i][j] = 'X';
                return board;
            } else {
                // 计算当前位置的周围有多少颗地雷
                int countMines = 0;
                for (int[] dir : dirs) {
                    int x = i + dir[0], y = j + dir[1];
                    if (x < 0 || y < 0 || x >= m || y >= n) continue;
                    if (board[x][y] == 'M') countMines++;
                }
                
                if (countMines == 0) {
                    board[i][j] = 'B';
                    for (int[] dir : dirs) {
                        int x = i + dir[0], y = j + dir[1];
                        if (x < 0 || y < 0 || x >= m || y >= n || board[x][y] != 'E') continue;
                        queue.offer(Arrays.asList(x, y));
                        // 提前设置为B防止重复加入而超时
                        // 反正之后如果周围没有地雷，那就是B，有地雷则会被设置为地雷数
                        board[x][y] = 'B';
                    }
                } else {
                    board[i][j] = (char)(countMines + '0');
                }
            }
        }
        
        return board;
    }
}
```

## Solution 2: DFS

- $TC:O(m*n)$
- $SC:O(m*n)$

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}, {1, 1}, {1, -1}, {-1, 1}, {-1,-1}};
    
    public char[][] updateBoard(char[][] board, int[] click) {
        if (board == null || board.length == 0 || board[0].length == 0) return board;
        
        int m = board.length, n = board[0].length;
        int i = click[0], j = click[1];
        if (board[i][j] == 'M') {
            board[i][j] = 'X';
            return board;
        } else {
            dfs(board, i, j, m, n);
        }
        
        return board;
    }
    
    private void dfs(char[][] board, int i, int j, int m, int n) {
        if (i < 0 || j < 0 || i >= m || j >= n || board[i][j] != 'E') return;
        
        int countMines = 0;
        for (int[] dir : dirs) {
            int x = i + dir[0], y = j + dir[1];
            if (x < 0 || y < 0 || x >= m || y >= n) continue;
            if (board[x][y] == 'M') countMines++;
        }
        
        if (countMines == 0) {
            board[i][j] = 'B';
            for (int[] dir : dirs) {
                int x = i + dir[0], y = j + dir[1];
                dfs(board, x, y, m, n);
            }
        } else {
            board[i][j] = (char)(countMines + '0');
        }
    }
}
```