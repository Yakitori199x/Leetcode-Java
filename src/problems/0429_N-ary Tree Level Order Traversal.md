# 429. N-ary Tree Level Order Traversal (2021.03.10)

https://leetcode.com/problems/n-ary-tree-level-order-traversal/

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

## Solution 1: BFS

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        if (root == null) return new LinkedList<>();
        
        List<List<Integer>> res = new LinkedList<>();
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            List<Integer> level = new LinkedList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Node cur = queue.poll();
                level.add(cur.val);
                for (Node child : cur.children) {
                    queue.offer(child);
                }
            }
            res.add(level);
        }
        return res;
    }
}
```

## Solution 2: DFS

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        // 由于外层的List需要被random access，所以使用ArrayList
        List<List<Integer>> res = new ArrayList<>();
        dfs(root, 0, res);
        return res;
    }
    
    private void dfs(Node root, int level, List<List<Integer>> res) {
        if (root == null) return;
        
        if (level == res.size()) res.add(new LinkedList<>());
        res.get(level).add(root.val);
        for (Node child : root.children) {
            dfs(child, level + 1, res);
        }
    }
}
```