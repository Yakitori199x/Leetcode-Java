## 1376. Time Needed to Inform All Employees (2020.03.21)

https://leetcode.com/problems/time-needed-to-inform-all-employees/

## Solution 1: Top-down DFS

- $TC:O(n)$
- $SC:O(n)$
- Construct graph from manager array, then do DFS on head node, dfs method returns the max distance from the node to its leaves

```java
class Solution {
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        for (int i = 0; i < manager.length; i++) {
            int j = manager[i];
            if (!graph.containsKey(j)) {
                graph.put(j, new ArrayList<>());
            }
            
            graph.get(j).add(i);
        }
        
        return dfs(graph, headID, informTime);
    }
    
    public int dfs(Map<Integer, List<Integer>> graph, int node, int[] informTime) {
        if (!graph.containsKey(node)) return 0;
        
        List<Integer> children = graph.get(node);
        int max = 0;
        for (int i = 0; i < children.size(); i++) {
            max = Math.max(max, dfs(graph, children.get(i), informTime));
        }
        
        return informTime[node] + max;
    }
}
```

## Solution 2: Bottom-up DFS

- $TC:O(n)$
- $SC:O(n)$
- Calculate the distance of each node from the root
- Turn the tree to forest by setting the node as new root

```java
class Solution {
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int res = 0;
        for (int i = 0; i < manager.length; i++) {
            res = Math.max(res, dfs(i, manager, informTime));
        }
        return res;
    }
    
    public int dfs(int node, int[] manager, int[] informTime) {
        if (manager[node] != -1) {
            informTime[node] += dfs(manager[node], manager, informTime);
            manager[node] = -1;
        }
        
        return informTime[node];
    }
}
```

## Solution 3: BFS

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        List<Integer>[] graph = new List[n];
        for (int i = 0; i < graph.length; i++) graph[i] = new ArrayList<>();
        for (int i = 0; i < graph.length; i++) {
            if (manager[i] != -1) graph[manager[i]].add(i);
        }
        
        int res = 0;
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{headID, 0});
        while (!queue.isEmpty()) {
            int[] pair = queue.poll();
            int node = pair[0];
            int time = pair[1];
            res = Math.max(res, time);
            for (int i = 0; i < graph[node].size(); i++) {
                queue.offer(new int[]{graph[node].get(i), time + informTime[node]});
            }
        }
        
        return res;
    }
}
```

