# 547. Number of Provinces (2021.02.27)

https://leetcode.com/problems/number-of-provinces/

## Solution 1: DFS

- $TC:O(N^2)$
- $SC:O(N)$
- 这题就是在求图的连通分量个数，可以通过DFS，BFS或是并查集求解。

```java
class Solution {
    public int findCircleNum(int[][] isConnected) {
        if (isConnected == null || isConnected.length == 0 || isConnected[0].length == 0) return 0;
        
        int n = isConnected.length, res = 0;
        boolean[] visited = new boolean[n];
        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(isConnected, i, visited);
                res++;
            }
        }
        
        return res;
    }
    
    private void dfs(int[][] isConnected, int node, boolean[] visited) {
        if (visited[node]) return;
        
        visited[node] = true;
        for (int i = 0; i < isConnected.length; i++) {
            if (isConnected[node][i] == 1) dfs(isConnected, i, visited);
        }
    }
}
```

## Solution 2: Union Find, complete ver

- $TC:O(N^2*α(n))$
- $SC:O(N)$
- 完整版并查集实现，带路径压缩和rank的优化。
- 关于并查集不同情况的复杂度分析，可见：https://leetcode-cn.com/problems/number-of-provinces/solution/jie-zhe-ge-wen-ti-ke-pu-yi-xia-bing-cha-0unne/

```java
class Solution {
    class UF {
        private int[] parent, rank;
        private int count;
        
        public UF(int n) {
            this.parent = new int[n];
            this.rank = new int[n];
            this.count = n;
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        
        public int find(int p) {
            while (p != parent[p]) {
                parent[p] = parent[parent[p]];
                p = parent[p];
            }
            return p;
        }
        
        public void union(int x, int y) {
            int rootX = find(x), rootY = find(y);
            if (rootX == rootY) return;
            
            count--;
            if (rank[rootX] > rank[rootY]) parent[rootY] = rootX;
            else if (rank[rootX] < rank[rootY]) parent[rootX] = rootY;
            else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
        }
        
        public int getCount() {
            return count;
        }
    }
    
    public int findCircleNum(int[][] isConnected) {
        if (isConnected == null || isConnected.length == 0 || isConnected[0].length == 0) return 0;
        
        int n = isConnected.length;
        UF uf = new UF(n);
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isConnected[i][j] == 1) uf.union(i, j);
            }
        }
        
        return uf.getCount();
    }
}
```

## Solution 3: Union Find, easy ver

- $TC:O(N^3)$
- $SC:O(N)$
- 简化版并查集实现，不带任何优化策略，只需要写一个额外的方法。
- 当然也可以将路径压缩等优化结合进去，但是如果要实现这种比较全面的版本建议像Solution2一样，额外写一个UF的类。

```java
class Solution {
    public int findCircleNum(int[][] isConnected) {
        if (isConnected == null || isConnected.length == 0 || isConnected[0].length == 0) return 0;
        
        int n = isConnected.length, res = 0;
        int[] parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isConnected[i][j] == 1) unionFind(i, j, parent);
            }
        }
        
        for (int i = 0; i < n; i++) {
            if (i == parent[i]) res++;
        }
        return res;
    }
    
    private void unionFind(int i, int j, int[] parent) {
        while (i != parent[i]) i = parent[i];
        while (j != parent[j]) j = parent[j];
        if (i != j) parent[i] = j;
    }
}
```