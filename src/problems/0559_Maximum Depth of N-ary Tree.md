## 559. Maximum Depth of N-ary Tree (2020.10.12)

https://leetcode.com/problems/maximum-depth-of-n-ary-tree/


```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/
```

## Solution 1: DFS

- $TC:O(N)$
- $SC:O(h)$

```java
class Solution {
    public int maxDepth(Node root) {
        if (root == null) return 0;
        
        int depth = 0;
        for (Node node : root.children) {
            depth = Math.max(depth, maxDepth(node));
        }
        
        return depth + 1;
    }
}
```

## Solution 2: BFS

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public int maxDepth(Node root) {
        if (root == null) return 0;
        
        int res = 0;
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Node node = queue.poll();
                for (Node child : node.children) {
                    queue.offer(child);
                }
            }
            
            res++;
        }
        
        return res;
    }
}
```

