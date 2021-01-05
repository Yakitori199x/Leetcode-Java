## 210. Course Schedule II (2020.10.25)

https://leetcode.com/problems/course-schedule-ii/

## Solution 1: BFS - Kahn's Algorithm

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$, 不算构建邻接表则为$O(|V|)$
- Similar to [207. Course Schedule]

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] topoOrder = new int[numCourses];
        List<List<Integer>> graph = new ArrayList<>();
        int[] indegree = new int[numCourses];
        
        for (int i = 0; i < numCourses; i++) graph.add(new LinkedList<>());
        
        for (int[] edge : prerequisites) {
            int from = edge[1], to = edge[0];
            graph.get(from).add(to);
            indegree[to]++;
        }
        
        Queue<Integer> zeroIn = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) zeroIn.offer(i);
        }
        
        int idx = 0;
        while (!zeroIn.isEmpty()) {
            int node = zeroIn.poll();
            for (int adjNode : graph.get(node)) {
                indegree[adjNode]--;
                if (indegree[adjNode] == 0) zeroIn.offer(adjNode);
            }
            topoOrder[idx++] = node;
        }
        
        if (idx != numCourses) return new int[0];
        return topoOrder;
    }
}
```

## Solution 2: DFS

- $TC:O(|V|+|E|)$
- $SC:O(|V|+|E|)$, 不算构建邻接表则为$SC:O(|V|)$，包括递归栈和返回的数组

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] topoOrder = new int[numCourses];
        List<List<Integer>> graph = new ArrayList<>();
        
        for (int i = 0; i < numCourses; i++) graph.add(new LinkedList<>());
        
        for (int[] edge : prerequisites) {
            int from = edge[1], to = edge[0];
            graph.get(from).add(to);
        }
        
        boolean[] inPath = new boolean[numCourses], visited = new boolean[numCourses];
        Deque<Integer> reverseOrder = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (dfs(i, graph, inPath, visited, reverseOrder)) {
                return new int[0];
            }
        }
        
        int idx = 0;
        while (!reverseOrder.isEmpty()) topoOrder[idx++] = reverseOrder.pop();
        
        return topoOrder;
    }
    
    // 返回true代表存在cycle，false代表未检测到cycle
    private boolean dfs(int node, List<List<Integer>> graph, boolean[] inPath, boolean[] visited, Deque<Integer> reverseOrder) {
        if (visited[node] == true) return false;
        if (inPath[node] == true) return true;
        
        inPath[node] = true;
        for (int adjNode : graph.get(node)) {
            if (dfs(adjNode, graph, inPath, visited, reverseOrder))
                return true;
        }
        
        inPath[node] = false;
        visited[node] = true;
        reverseOrder.push(node);
        return false;
    }
}
```

#### Reference
- https://www.youtube.com/watch?v=eL-KzMXSXXI&t=8s
- https://www.youtube.com/watch?v=cIBFEhD77b4
