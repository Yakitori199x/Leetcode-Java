# 590. N-ary Tree Postorder Traversal (2021.03.10)

https://leetcode.com/problems/n-ary-tree-postorder-traversal/

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
    public List<Integer> postorder(Node root) {
        List<Integer> res = new LinkedList<>();
        postorder(root, res);
        return res;
    }
    
    private void postorder(Node root, List<Integer> res) {
        if (root == null) return;
        
        for (Node child : root.children) {
            postorder(child, res);
        }
        res.add(root.val);
    }
}
```

## Solution 2: Iterative (reverse)

- $TC:O(n)$
- $SC:O(n)$
- 后序遍历就是类前序遍历的reverse。
- 类前序遍历：前序遍历的Iterative实现就是将下一层的node从右到左压入栈，而类前序遍历刚好是相反的顺序。

```java
class Solution {
    public List<Integer> postorder(Node root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) return res;
        
        Deque<Node> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            Node cur = stack.pop();
            res.add(cur.val);
            for (Node child : cur.children) {
                stack.push(child);
            }
        }
        
        Collections.reverse(res);
        return res;
    }
}
```

## Solution 3: Iterative

- $TC:O(n)$
- $SC:O(h)$
- Iterative的非reverse版本。
- 关键就是用一个Pair记录结点及其对应的已遍历过的子结点的index。
- 当node的所有子结点都被遍历过时（即index = node.children.size()），就可以把node的值添加到res中。
- 否则，说明node还有未遍历的子结点（子树），那么就需要先把对应index的子结点压入栈。

```java
class Solution {
    public List<Integer> postorder(Node root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) return res;
        
        Deque<Pair<Node, Integer>> stack = new LinkedList<>();
        stack.push(new Pair(root, 0));
        while (!stack.isEmpty()) {
            // 这里直接pop出来是因为Integer是immutable的且Pair没有set方法
            // 所以需要先pop，然后有需要的话重新压入相同结点的index+1的Pair
            Pair<Node, Integer> pair = stack.pop();
            Node cur = pair.getKey();
            int index = pair.getValue();
            
            // index和cur的子结点数相等，说明子结点均被遍历，那么postorder的下一个就是cur结点
            if (index == cur.children.size()) {
                res.add(cur.val);
            } else {
                stack.push(new Pair(cur, index + 1));
                stack.push(new Pair(cur.children.get(index), 0));
            }
        }
        
        return res;
    }
}
```