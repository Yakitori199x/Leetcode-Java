# 1162. As Far from Land as Possible (2021.03.09)

https://leetcode.com/problems/as-far-from-land-as-possible/

## Similar Questions
- 542. 01 Matrix

## Solution 1: BFS

- $TC:O(n^2)$
- $SC:O(n^2)$
- 把所有陆地（即值为1的格子）加入到队列中，以此为初始状态进行BFS，计算出各个海洋（值为0的格子）到陆地的最近距离。
- 其中最大的距离就是我们所求的答案。

```java
class Solution {
    private int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    
    public int maxDistance(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return -1;
        
        int n = grid.length;
        boolean[][] visited = new boolean[n][n];
        Queue<List<Integer>> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    queue.offer(Arrays.asList(i, j));
                    visited[i][j] = true;
                }
            }
        }
        
        // 设置初始值为-1，这样最后返回的时候就不需要减去1
        int dist = -1;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int k = 0; k < size; k++) {
                List<Integer> pos = queue.poll();
                int i = pos.get(0), j = pos.get(1);
                for (int[] dir : dirs) {
                    int x = i + dir[0], y = j + dir[1];
                    if (x < 0 || y < 0 || x >= n || y >= n || visited[x][y]) continue;
                    queue.offer(Arrays.asList(x, y));
                    visited[x][y] = true;
                }
            }
            dist++;
        }

        // 当全为陆地时，dist=0，全为海洋时，dist=-1，这两种情况都要返回-1
        return dist <= 0 ? -1 : dist;
    }
}
```

## Solution 2: DP

- $TC:O(n^2)$
- $SC:O(n^2)$，如果是in-place把grid修改成dist，那么不需要额外空间。
- 和LC542的DP解法类似，dist[i][j]表示从(i,j)到陆地的最近距离。
- 且有dist[i][j] = min(dist[i-1][j], dist[i][j-1], dist[i+1][j], dist[i][j+1]) + 1
- 这些dist[i][j]中的最大值就是我们要的答案。

```java
class Solution {
    public int maxDistance(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return -1;
        
        int n = grid.length, maxDist = 0, limit = 2 * n;
        int[][] dist = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) dist[i][j] = 0;
                else {
                    // 此处直接先给海洋格子的dist设置成limit
                    // 之后再根据边界是否在grid的范围内来决定是否依据up和left的dist更新dist[i][j]
                    dist[i][j] = limit;
                    if (i > 0) dist[i][j] = Math.min(dist[i][j], dist[i - 1][j] + 1);
                    if (j > 0) dist[i][j] = Math.min(dist[i][j], dist[i][j - 1] + 1);
                }
            }
        }
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                if (grid[i][j] == 1) dist[i][j] = 0;
                else {
                    // 这里是第二次循环，不需要再对dist[i][j]设置limit为初始值，直接根据边界进行更新
                    // 最后再查看更新后的dist[i][j]是否是最大的
                    if (i < n - 1) dist[i][j] = Math.min(dist[i][j], dist[i + 1][j] + 1);
                    if (j < n - 1) dist[i][j] = Math.min(dist[i][j], dist[i][j + 1] + 1);
                    maxDist = Math.max(maxDist, dist[i][j]);
                }
            }
        }
        
        return maxDist == limit || maxDist == 0 ? -1 : maxDist;
    }
}
```