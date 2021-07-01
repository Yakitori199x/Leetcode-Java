# 417. Pacific Atlantic Water Flow (2021.02.23)

https://leetcode.com/problems/pacific-atlantic-water-flow/

## Solution 1: BFS

- $TC:O(m*n)$
- $SC:O(m*n)$
- 本题不适合用DP。比如在计算Pacific的DP时，所求位置(i,j)能否到达Pacific不仅仅取决于左侧和上方的点。
- 比如[[1,2,3], [8,9,4], [7,6,5]]，对于(2,1)这个位置，其可行的路线应该是从右侧(5)推出来的，只关注左侧和上方就会判断成false。

```java
class Solution {
    private int[][] direct = new int[][]{{-1, 0}, {1, 0}, {0, 1}, {0, -1}};
    
    public List<List<Integer>> pacificAtlantic(int[][] matrix) {
        List<List<Integer>> res = new ArrayList<>();
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return res;
        
        int m = matrix.length, n = matrix[0].length;
        // 两个boolean数组分别表示从位置(i,j)是否能到达对应的大洋
        boolean[][] pacific = new boolean[m][n];
        boolean[][] atlantic = new boolean[m][n];
        Queue<List<Integer>> pQueue = new LinkedList<>();
        Queue<List<Integer>> aQueue = new LinkedList<>();
        
        // 垂直方向，左侧的点都可以流入Pacific，右侧的点都可以流入Atlantic
        for (int i = 0; i < m; i++) {
            pacific[i][0] = true;
            atlantic[i][n - 1] = true;
            pQueue.add(Arrays.asList(i, 0));
            aQueue.add(Arrays.asList(i, n - 1));
        }
        
        // 水平方向，上方的点都可以流入Pacific，下方的点都可以流入Atlantic
        for (int j = 0; j < n; j++) {
            pacific[0][j] = true;
            atlantic[m - 1][j] = true;
            pQueue.add(Arrays.asList(0, j));
            aQueue.add(Arrays.asList(m - 1, j));
        }
        
        // 对双方分别进行BFS
        bfs(matrix, pacific, pQueue);
        bfs(matrix, atlantic, aQueue);
        
        // 当pacific[i][j]和atlantic[i][j]均为true，加入最终的res
        // 即(i,j)既能流入Pacific，又能流入Atlantic
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (pacific[i][j] && atlantic[i][j]) res.add(Arrays.asList(i, j));
            }
        }
        return res;
    }
    
    private void bfs(int[][] matrix, boolean[][] isValid, Queue<List<Integer>> queue) {
        int m = matrix.length, n = matrix[0].length;
        while (!queue.isEmpty()) {
            List<Integer> coord = queue.poll();
            // direct记录了上下左右四个方向的offset，便于搜索与当前位置相邻的四个不同方位的点
            for (int i = 0; i < direct.length; i++) {
                int x = coord.get(0) + direct[i][0], y = coord.get(1) + direct[i][1];
                // 如果被搜索的点超过了matrix的范围，或是已经确定为可以流入对应大洋的位置
                // 抑或是对应的高度比之前的位置要低（说明水流无法从新的位置流入之前的位置）
                // 都可以直接略过该位置的后续处理
                if (x < 0 || y < 0 || x >= m || y >= n || isValid[x][y] 
                    || matrix[x][y] < matrix[coord.get(0)][coord.get(1)]) continue;
                
                // 否则，说明水流可以流入之前的位置，而之前的位置是一个有效的位置
                // 可以推理出该位置也是一个有效的位置
                isValid[x][y] = true;
                queue.add(Arrays.asList(x, y));
            }
        }
    }
}
```

## Solution 2: DFS

- $TC:O(m*n)$
- $SC:O(m*n)$

```java
class Solution {
    private int[][] direct = new int[][]{{-1, 0}, {1, 0}, {0, 1}, {0, -1}};
    
    public List<List<Integer>> pacificAtlantic(int[][] matrix) {
        List<List<Integer>> res = new ArrayList<>();
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return res;
        
        int m = matrix.length, n = matrix[0].length;
        boolean[][] pacific = new boolean[m][n];
        boolean[][] atlantic = new boolean[m][n];
        
        // 只需要以四个边的点为起始点进行DFS
        // 因为DFS会把能通过该点流入海洋的所有点都标记好，不需要再去以内部点为起始点做DFS
        for (int i = 0; i < m; i++) {
            dfs(matrix, pacific, i, 0);
            dfs(matrix, atlantic, i, n - 1);
        }
        
        for (int j = 0; j < n; j++) {
            dfs(matrix, pacific, 0, j);
            dfs(matrix, atlantic, m - 1, j);
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (pacific[i][j] && atlantic[i][j]) res.add(Arrays.asList(i, j));
            }
        }
        return res;
    }
    
    private void dfs(int[][] matrix, boolean[][] isValid, int i, int j) {
        // 不加也可以，加了的话可以使得在前一轮DFS中满足条件且被标记了的边上的点可以直接返回
        // 比如前一轮是对(0,0)进行DFS，在这个过程中(0,1)满足条件可以通过(0,0)流入海洋
        // 那么就会标记(0,1)并基于(0,1)继续做DFS，也就是说(0,1)的DFS已经顺带在(0,0)这一轮做掉了
        if (isValid[i][j]) return;
        
        isValid[i][j] = true;
        int m = matrix.length, n = matrix[0].length;
        for (int[] dir :direct) {
            int x = i + dir[0], y = j + dir[1];
            if (x < 0 || y < 0 || x >= m || y >= n || isValid[x][y]
                || matrix[x][y] < matrix[i][j]) continue; 
            dfs(matrix, isValid, x, y);
        }
    }
}
```