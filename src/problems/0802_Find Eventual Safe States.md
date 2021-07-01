# 802. Find Eventual Safe States (2021.02.15)

https://leetcode.com/problems/find-eventual-safe-states/

## Solution 1: DFS, Coloring

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$
- 这道题本质上就是判断从某个结点出发，该结点所有可能的path是否都不会存在环，可以说是一道有向图相关的环判断问题。
- 比如graph=[[1],[2],[1,3],[]]时，即0->1->2->3，2->1这样一幅图，由于2->1这条path形成了环。
- 所以虽然0，1，2都可以到达安全结点3，但是并不能确保它们能够在有限步内到达，或者说这几个结点存在有环的path。
- 使用染色的方法，给每个结点赋予三种状态，0表示结点尚未被遍历到，1表示已经确定该结点为安全结点，-1表示遍历过该结点但还不能确定是否安全。
- 之后对每一个结点做DFS即可，如果发现某条path返回false，说明该path上存在环，不可能通向安全结点。
- 可以直接返回false，不必再搜索其他的路径。

```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        if (graph == null || graph.length == 0) return new ArrayList<>();
        
        List<Integer> res = new ArrayList<>();
        int n = graph.length;
        int[] colors = new int[n];
        for (int i = 0; i < n; i++) {
            if (dfs(graph, i, colors)) res.add(i);
        }
        
        return res;
    }
    
    private boolean dfs(int[][] graph, int node, int[] colors) {
        if (colors[node] != 0) return colors[node] == 1;
        
        colors[node] = -1;
        for (int neighbor : graph[node]) {
            if (!dfs(graph, neighbor, colors)) return false;
        }
        
        colors[node] = 1;
        return true;
    }
}
```

## Solution 2: Topological Sort (Reverse Edges)

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$
- 拓扑逻辑排序可以用于判断graph中是否存在环，也可以用于判断以graph中的某个node为起点，是否存在path会形成环。
- 从outdegree为0的nodes（即terminal nodes）开始，删除其所有的入边，并判断通过入边与其连接的node是否还存在out-edges。
- 如果在删除后，某个in-node不存在out-edges，说明从这个node出发的所有path最终都会通往safe node或terminal node，则其自身也是safe的。
- 反之，如果到最后还存在out-edges，则说明存在某条path形成了环。

```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        if (graph == null || graph.length == 0) return null;
        
        int n = graph.length;
        int[] outDegrees = new int[n];
        List<List<Integer>> rgraph = new ArrayList<>();
        for (int i = 0; i < n; i++) rgraph.add(new ArrayList<>());
        
        // 构造reverse graph，即记录node和其对应的in-nodes
        // 同时构造每个node的outdegree，以及将首批outdegree为0的node加入队列
        Queue<Integer> queue = new LinkedList<>();
        boolean[] isSafe = new boolean[n];
        for (int i = 0; i < n; i++) {
            for (int neighbor : graph[i]) {
                outDegrees[i]++;
                rgraph.get(neighbor).add(i);
            }
            
            if (outDegrees[i] == 0) queue.offer(i);
        }
        
        // 删除node对应的in-edges，并判断删除后的in-nodes是否成为新的candidates
        // 这里可以简单地通过减少in-nodes的outdegree来实现
        while (!queue.isEmpty()) {
            int node = queue.poll();
            isSafe[node] = true;
            for (int neighbor : rgraph.get(node)) {
                outDegrees[neighbor]--;
                if (outDegrees[neighbor] == 0) queue.offer(neighbor);
            }
        }
        
        // isSafe记录了每个结点是否是safe的，节省了一些排序的开销
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (isSafe[i] == true) res.add(i);
        }
        return res;
    }
}
```