# 785. Is Graph Bipartite? (2021.02.20)

https://leetcode.com/problems/is-graph-bipartite/

## Solution 1: DFS, Coloring

- $TC:O(|V|+|E|)$，因为可能有多个连通分量，需要遍历所有结点
- $SC:O(|V|)$
- 二分图，染色问题。

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n];
        
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0) {
                if (!isBipartite(graph, i, colors, 1)) return false;
            }
        }
        
        return true;
    }
    
    private boolean isBipartite(int[][] graph, int node, int[] colors, int color) {
        // 待染颜色和已染颜色不一致，说明发生冲突，返回false，否则返回true继续遍历
        if (colors[node] != 0) return colors[node] == color;
        
        colors[node] = color;
        for (int neighbor : graph[node]) {
            if (!isBipartite(graph, neighbor, colors, -color)) {
                return false;
            }
        }
        
        return true;
    }
}
```

## Solution 2: BFS, Coloring

- $TC:O(|V|+|E|)$，因为可能有多个连通分量，需要遍历所有结点
- $SC:O(|V|)$

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n];
        
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0) {
                colors[i] = 1;
                queue.add(i);
                while (!queue.isEmpty()) {
                    int node = queue.poll();
                    for (int neighbor : graph[node]) {
                        if (colors[neighbor] == 0) {
                            colors[neighbor] = -colors[node];
                            queue.add(neighbor);
                        }
                        
                        if (colors[neighbor] != -colors[node]) {
                            return false;
                        }
                    }
                }
            }
        }
        
        return true;
    }
}
```