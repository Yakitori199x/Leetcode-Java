# 589. N-ary Tree Preorder Traversal (2021.03.10)

https://leetcode.com/problems/n-ary-tree-preorder-traversal/

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

## Solution 1: Recursive

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> res = new LinkedList<>();
        preorder(root, res);
        return res;
    }
    
    private void preorder(Node root, List<Integer> res) {
        if (root == null) return;
        
        res.add(root.val);
        for (Node child : root.children) {
            preorder(child, res);
        }
    }
}
```

## Solution 2: Iterative

- $TC:O(n)$
- $SC:O(n)$，因为是N-ary，所以可能某一层有很多结点

```java
class Solution {
    public List<Integer> preorder(Node root) {
        if (root == null) return new LinkedList<>();
        
        List<Integer> res = new LinkedList<>();
        Deque<Node> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            Node cur = stack.pop();
            res.add(cur.val);
            for (int i = cur.children.size() - 1; i >= 0; i--) {
                stack.push(cur.children.get(i));
            }
        }
        return res;
    }
}
```