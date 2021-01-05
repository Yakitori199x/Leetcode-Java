## 1306. Jump Game III (2020.12.06)

https://leetcode.com/problems/jump-game-iii/

## Solution 1: BFS

- $TC: O(n)$
- $SC: O(n)$
- n为arr的长度。
- 本题实际上是一个有向图的遍历问题，且该有向图中每个结点的出度至多为2（因为有可能跳出界）。
- 为了避免遇到环造成无限循环，需要visited来记录对应结点（index）是否已经被遍历。

```java
class Solution {
    public boolean canReach(int[] arr, int start) {
        if (arr == null || arr.length == 0) return false;
        
        boolean[] visited = new boolean[arr.length];
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(start);
        
        while (!queue.isEmpty()) {
            int node = queue.poll();
            if (arr[node] == 0) return true;
            if (visited[node]) continue;
            
            visited[node] = true;
            if (node + arr[node] < arr.length) queue.offer(node + arr[node]);
            if (node - arr[node] >= 0) queue.offer(node - arr[node]);
        }
        
        return false;
    }
}
```

## Solution 2: DFS

- $TC: O(n)$
- $SC: O(n)$
- n为arr的长度。

```java
class Solution {
    public boolean canReach(int[] arr, int start) {
        if (arr == null || arr.length == 0) return false;
        
        boolean[] visited = new boolean[arr.length];
        return canReach(arr, start, visited);
    }
    
    private boolean canReach(int[] arr, int start, boolean[] visited) {
        if (start < 0 || start >= arr.length) return false;
        if (arr[start] == 0) return true;
        if (visited[start]) return false;
        
        visited[start] = true;
        return canReach(arr, start + arr[start], visited) || canReach(arr, start - arr[start], visited);
    }
}
```

