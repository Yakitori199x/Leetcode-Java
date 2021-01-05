## 332. Reconstruct Itinerary (2020.10.21)

https://leetcode.com/problems/reconstruct-itinerary/

## Solution 1: DFS - Recursive

- $TC:O(E)$
- $SC:O(E)$
- Eulerian Path/Circuit algorithm (Hierholzer's algorithm)

```java
class Solution {
    
    private Map<String, Queue<String>> graph;
    private List<String> path;
    
    public List<String> findItinerary(List<List<String>> tickets) {
        graph = new HashMap<>();
        path = new LinkedList<>();
        
        for (List<String> ticket : tickets) {
            // 使用优先队列来构造邻接表，确保lexical order
            graph.putIfAbsent(ticket.get(0), new PriorityQueue<>());
            graph.get(ticket.get(0)).add(ticket.get(1));
        }
        
        dfs("JFK");
        return path;
    }
    
    private void dfs(String node) {
        // 要检查graph是否包含该结点，因为可能有结点只有入度没有出度
        while (graph.containsKey(node) && !graph.get(node).isEmpty()) {
            // 一边递归一边将结点弹出
            dfs(graph.get(node).poll());
        }
        
        path.add(0, node);
    }
}
```

## Solution 2: DFS - Iterative

- $TC:O(E)$
- $SC:O(E)$

```java
class Solution {
    
    private Map<String, Queue<String>> graph;
    private List<String> path;
    
    public List<String> findItinerary(List<List<String>> tickets) {
        graph = new HashMap<>();
        path = new LinkedList<>();
        
        for (List<String> ticket : tickets) {
            graph.putIfAbsent(ticket.get(0), new PriorityQueue<>());
            graph.get(ticket.get(0)).add(ticket.get(1));
        }
        
        Deque<String> stack = new LinkedList<>();
        stack.push("JFK");
        
        while (!stack.isEmpty()) {
            while (graph.containsKey(stack.peek()) && !graph.get(stack.peek()).isEmpty()) {
                stack.push(graph.get(stack.peek()).poll());
            }
            
            path.add(0, stack.pop());
        }
        
        return path;
    }
}
```

#### Reference
- https://www.youtube.com/watch?v=xR4sGgwtR2I
- https://www.youtube.com/watch?v=8MpoO2zA2l4
- https://www.youtube.com/watch?v=QQ3jO1dKjYQ
- https://nan-zhou.com/index.php/2020/06/28/eulerian-path/
