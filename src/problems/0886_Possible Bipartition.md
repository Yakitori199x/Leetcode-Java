# 886. Possible Bipartition (2021.02.14)

https://leetcode.com/problems/possible-bipartition/

## Solution 1: DFS, Coloring

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$
- 可以把N个人抽象为图的N个结点，dislike的关系抽象为图中连接两个结点的边。
- 要判断是否能够将这组人分成两个小组，且各自小组中的人没有互相讨厌，就等价于求对应的图模型是否能够被二分。
- 因此，该题被转化为图的二分问题，可以使用染色的方法求解（二分图中属于同一组的结点不能有边连接）。
- 即有两种颜色，从某个结点开始染上其中一种颜色，然后找到它的相邻结点，染上另一种颜色，以此类推。
- 如果发现有相邻结点已经被染色，且其颜色和即将给它染的颜色不同时，说明出现冲突，该图不能被二分。

```java
class Solution {
    public boolean possibleBipartition(int N, int[][] dislikes) {
        List<List<Integer>> graph = new ArrayList<>();
        // 准备构造邻接表，因为输入数据中结点从1开始，index=0为dummy
        for (int i = 0; i <= N; i++) graph.add(new ArrayList<>());
        
        for (int[] dislike : dislikes) {
            graph.get(dislike[0]).add(dislike[1]);
            graph.get(dislike[1]).add(dislike[0]);
        }
        
        int[] colors = new int[N + 1];
        // 如果graph只有一个连通分量，那么对其中任意一个结点dfs就足够遍历所有结点了
        // 但是有可能graph有多个连通分量，所以需要对所有结点都遍历一次
        for (int i = 1; i <= N; i++) {
            // 当前结点没有被染色时进行dfs
            // 如果dfs过程出现冲突，可以立即返回false说明不存在二分图
            if (colors[i] == 0 && !dfs(graph, i, colors, 1)) return false;
        }
        
        // 对所有结点的dfs过程都没有发现冲突，说明可以形成二分图
        return true;
    }
    
    private boolean dfs(List<List<Integer>> graph, int node, int[] colors, int color) {
        // 给当前node染色
        colors[node] = color;
        
        List<Integer> neighbors = graph.get(node);
        for (int neighbor : neighbors) {
            // 如果发现相邻结点的颜色和待染颜色不同，说明发生冲突，直接返回false
            if (colors[neighbor] == color) return false;
            if (colors[neighbor] == 0 && !dfs(graph, neighbor, colors, -color)) return false;
        }
        
        return true;
    }
}
```

## Solution 2: BFS

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$
- 用BFS也可以解决染色问题。

```java
class Solution {
    public boolean possibleBipartition(int N, int[][] dislikes) {
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i <= N; i++) graph.add(new ArrayList<>());
        
        for (int[] dislike : dislikes) {
            graph.get(dislike[0]).add(dislike[1]);
            graph.get(dislike[1]).add(dislike[0]);
        }
        
        int[] colors = new int[N + 1];
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 1; i <= N; i++) {
            // 一般来说BFS会用一个visited来记录node是否被遍历过
            // 不过此处我们直接使用color来判断node是否需要遍历以及是否需要加入queue
            if (colors[i] == 0) {
                // 注意将node加入queue同时将其染色
                // 这样在遍历node的邻接结点时才能设定相反的颜色
                queue.add(i);
                colors[i] = 1;
                while (!queue.isEmpty()) {
                    int node = queue.poll();
                    List<Integer> neighbors = graph.get(node);
                    for (int neighbor : neighbors) {
                        // 颜色冲突，返回false
                        if (colors[neighbor] == colors[node]) return false;
                        
                        // 还未被染色，设定颜色后加入queue，等待遍历
                        if (colors[neighbor] == 0) {
                            colors[neighbor] = -colors[node];
                            queue.add(neighbor);
                        }

                        // 否则说明已经被染色且颜色正确（正在queue中等待被遍历，或是已经被visited）
                        // 不必做其他的处理
                    }
                }
            }
        }
        
        return true;
    }
}
```