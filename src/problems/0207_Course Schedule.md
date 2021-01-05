## 207. Course Schedule (2020.10.14)

https://leetcode.com/problems/course-schedule/

## Solution 1: BFS - Topological sort

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$
- 拓扑排序，能够判断有向图是否存在cycle，同时生成一个结点的序列，且所有结点的边的指向都是单向的。
- 本题基于邻接表来实现，并记录每个结点的indegree。若indegree为0，说明该结点处不可能存在环。
- 所以需要做的就是将入度为0结点及其所有出边删除（修改出边连接结点的入度），如此反复直到不存在入度为0的结点为止。
- 此时若入度为0的结点数小于结点总数，即说明存在环。否则输出的顶点序列就是一种拓扑序列，即可以完成所有任务。

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<List<Integer>> adj = new ArrayList<>();
        int[] inDegree = new int[numCourses];
        
        for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
        
        for (int i = 0; i < prerequisites.length; i++) {
            int w = prerequisites[i][0], v = prerequisites[i][1];
            adj.get(v).add(w);
            inDegree[w]++;
        }
        
        Queue<Integer> queue = new LinkedList<>();
        int noIndegree = 0;
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) queue.offer(i);
        }
        
        while (!queue.isEmpty()) {
            int v = queue.poll();
            noIndegree++;
            for (int w : adj.get(v)) {
                if (--inDegree[w] == 0) queue.offer(w);
            }
        }
        
        return noIndegree == numCourses;
    }
}
```

## Solution 2: DFS

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$
- DFS只能用来判断有向图中是否存在环
- 如果需要生成拓扑排序，还需要在确认不存在环的情况下进行Reverse postorder的DFS（即在递归调用之后将当前结点入栈）

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        List<List<Integer>> adj = new ArrayList<>();
        
        for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
        
        for (int i = 0; i < prerequisites.length; i++) {
            int w = prerequisites[i][0], v = prerequisites[i][1];
            adj.get(v).add(w);
        }
        
        // marked用来记录结点在dfs中是否被遍历过，即是否在当前dfs对应的path中出现过，用来判断是否存在环
        // done用来记录在对某个结点进行dfs后，若从该结点开始的dfs对应path不存在环，则此后的dfs如果又遍历到该结点则无需继续遍历下去，可以减少计算量
        boolean[] marked = new boolean[numCourses], done = new boolean[numCourses];
        for (int v = 0; v < numCourses; v++) {
            if (!dfs(v, adj, marked, done)) return false;
        }
        
        return true;
    }
    
    private boolean dfs(int v, List<List<Integer>> adj, boolean[] marked, boolean[] done) {
        if (done[v] == true) return true;
        if (marked[v] == true) return false;
        
        // mark当前结点v，使后续dfs递归有能力检测出环
        marked[v] = true;
        
        for (int w : adj.get(v)) {
            if (!dfs(w, adj, marked, done)) {
                return false;
            }
        }
        
        // 由于dfs先判断了done才判断marked，所以可以不需要将marked还原
        // 否则需要将marked还原，不然之后dfs又遇到这个结点时会认为存在环
        marked[v] = false;
        done[v] = true;
        return true;
    }
}
```

