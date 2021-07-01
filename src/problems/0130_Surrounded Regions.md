# 130. Surrounded Regions (2021.03.02)

https://leetcode.com/problems/surrounded-regions/

## Solution 1: DFS

- $TC:O(m*n)$
- $SC:O(m*n)$
- 这题的关键在于从四个边进行DFS或者BFS，把与边上的O相连的整个连通分量标记成*（同时起到了visited的作用）。
- 之后对整个board进行遍历，这个时候还存在board上的O就代表它们不与四条边上的O相连且被X环绕，可以翻转成X。
- 而对于被标记成*的位置，我们将其恢复成O即可。
- DFS解法的一个缺点在于可能会有情况使得递归栈太深，造成StackOverflow。

```
比如这个例子，一旦从边上的O开始DFS，可能会顺着O的这一连串路径一直递归下去。
当m和n足够大时，就会StackOverflow。
OOOOOOOOOO
XXXXXXXXXO
OOOOOOOOOO
OXXXXXXXXX
OOOOOOOOOO

当然你可能会想，我们就不要从边上DFS。
而是比如判断(i,0)是否为O，然后再从(i,1)开始DFS，并且遍历到倒数第二列就停下。
毕竟边上的位置我们本来就要走一遍。但可惜的是，仍有机会StackOverflow：
OOOOOOOOOOOX
XXXXXXXXXXOX
XOOOOOOOOOOX
XOXXXXXXXXXX
XOOOOOOOOOOX

再过分一点就是基本上全图都是O，这也是为何本题的空间复杂度会达到O(m*n)。
具体分析见：（returnans的评论）
https://leetcode.com/problems/surrounded-regions/discuss/41612/A-really-simple-and-readable-C%2B%2B-solutionuff0conly-cost-12ms
```

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    
    public void solve(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) return;
        
        int m = board.length, n = board[0].length;
        if (m < 3 || n < 3) return;
        
        for (int i = 0; i < m; i++) {
            if (board[i][0] == 'O') dfs(board, i, 0);
            if (board[i][n - 1] == 'O') dfs(board, i, n - 1);
        }
        
        for (int j = 1; j < n - 1; j++) {
            if (board[0][j] == 'O') dfs(board, 0, j);
            if (board[m - 1][j] == 'O') dfs(board, m - 1, j);
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') board[i][j] = 'X';
                if (board[i][j] == '*') board[i][j] = 'O';
            }
        }
    }
    
    private void dfs(char[][] board, int i, int j) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != 'O') return;
        
        board[i][j] = '*';
        for (int[] dir : dirs) {
            int x = i + dir[0], y = j + dir[1];
            dfs(board, x, y);
        }
    }
}
```

## Solution 2: BFS

- $TC:O(m*n)$
- $SC:O(sqrt(m^2+n^2))$，即board的对角线上的所有元素均在queue中的情况，一般来说感觉是$SC:O(max(m,n))$
- 相比于DFS，BFS的优势就在于空间上，因为加入队列的点可以及时得到释放，而不会一路累加下去。
- 这里的BFS是对位于四条边上的每个O进行BFS。还可以一次性把边上所有的O都加入队列，然后开始BFS。

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    
    public void solve(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) return;
        
        int m = board.length, n = board[0].length;
        if (m < 3 || n < 3) return;
        
        for (int i = 0; i < m; i++) {
            if (board[i][0] == 'O') bfs(board, i, 0);
            if (board[i][n - 1] == 'O') bfs(board, i, n - 1);
        }
        
        for (int j = 1; j < n - 1; j++) {
            if (board[0][j] == 'O') bfs(board, 0, j);
            if (board[m - 1][j] == 'O') bfs(board, m - 1, j);
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') board[i][j] = 'X';
                if (board[i][j] == '*') board[i][j] = 'O';
            }
        }
    }
    
    private void bfs(char[][] board, int i, int j) {
        Queue<List<Integer>> queue = new LinkedList<>();
        
        board[i][j] = '*';
        queue.add(Arrays.asList(i, j));
        while (!queue.isEmpty()) {
            List<Integer> pos = queue.poll();
            for (int[] dir : dirs) {
                int x = pos.get(0) + dir[0], y = pos.get(1) + dir[1];
                if (x >= 0 && x < board.length && y >= 0 && y < board[0].length && board[x][y] == 'O') {
                    board[x][y] = '*';
                    queue.add(Arrays.asList(x, y));
                }
            }
        }
    }
}
```