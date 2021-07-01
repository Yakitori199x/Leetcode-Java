# 684. Redundant Connection (2021.02.16)

https://leetcode.com/problems/redundant-connection/

## Solution 1: DFS

- $TC:O(N^2)$
- $SC:O(N)$
- 每次进行DFS判断u和v是否连通的时间复杂度为O(E)，但是因为本题的graph是一棵树添上一条边，即E=N，N为结点数，所以最终为N相关的函数。
- 本题本质上是让你去找出构成环的其中一条边，不过加上了一些限制，比如u一定要小于v，且这条边在edges的顺序是所有candidates的最后一个。
- 如果没有这些限制，只需要构造好graph然后走一遍DFS找出环上的一个结点即可。
- 当然，即使有这些限制也可以用这种方法解，但是要做很多其他的处理，会使代码变得很复杂。
- 另一种思路就是从连通性下手。我们可以从所有结点均不连通（即不存在edge）的情况开始，一条一条地往graph里添加edge。
- 按理说在edge被添加到graph之前，u和v应该是不连通的，这种情况下我们就可以把edge加入使其连通。
- 如果发现在edge加入之前u和v就是连通的，那么这条edge的加入必定会生成环，即我们要求的答案。

```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        int N = edges.length;
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i <= N; i++) graph.add(new ArrayList<>());
        
        int[] res = null;
        for (int[] edge : edges) {
            int u = edge[0], v = edge[1];
            if (!dfs(u, v, 0, graph)) {
                graph.get(u).add(v);
                graph.get(v).add(u);
            } else {
                res = edge;
            }
        }
        
        return res;
    }
    
    private boolean dfs(int u, int v, int prev, List<List<Integer>> graph) {
        if (u == v) return true;
        
        for (int neighbor : graph.get(u)) {
            if (neighbor == prev) continue;
            if (dfs(neighbor, v, u, graph)) return true;
        }
        
        return false;
    }
}
```

## Solution 2: Union-Find

- $TC:O(N*α(N))$，$α(N)$是Inverse-Ackermann function，通过优化可以达到$O(1)$
- $SC:O(N)$
- 涉及到连通性问题，我们就可以考虑并查集。
- 本题可以将所有结点看作不同的连通分量作为初始状态，遍历每一条edge并将u和v连通起来。
- 同样地，如果在遍历的过程中发现u和v的root相同，即u和v在通过这条edge进行union之前就连通了，说明edge的加入会形成环。

```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        int N = edges.length;
        UnionFind uf = new UnionFind(N);
        
        for (int[] edge :edges) {
            int u = edge[0], v = edge[1];
            // 由于union的过程肯定要用到find
            // 可以在union中判断root是否相等并返回
            if (!uf.union(u, v)) return edge;
        }
        
        return null;
    }
    
    class UnionFind {
        // 记录每个结点对应的父结点
        private int[] parent;
        // 衡量以每个结点为root的树的深度，这里的实现不大准确
        private int[] rank;
        
        public UnionFind(int N) {
            this.parent = new int[N + 1];
            this.rank = new int[N + 1];
        }
        
        public int find(int u) {
            if (parent[u] == 0) return u;
            // path compression
            parent[u] = find(parent[u]);
            return parent[u];
        }
        
        public boolean union(int u, int v) {
            int rootU = find(u);
            int rootV = find(v);
            if (rootU == rootV) return false;
            
            // 注意这里是设置rootU和rootV的parent，不是u和v
            if (rank[rootU] > rank[rootV]) parent[rootV] = rootU;
            else if (rank[rootU] < rank[rootV]) parent[rootU] = rootV;
            else {
                parent[rootU] = rootV;
                rank[rootV]++;
            }
            
            return true;
        }
    }
}
```