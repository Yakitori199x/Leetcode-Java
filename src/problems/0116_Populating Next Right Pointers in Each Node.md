## 116. Populating Next Right Pointers in Each Node (2020.10.06)

https://leetcode.com/problems/populating-next-right-pointers-in-each-node/


```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node next;

    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, Node _left, Node _right, Node _next) {
        val = _val;
        left = _left;
        right = _right;
        next = _next;
    }
};
*/
```

## Solution 1: BFS (queue)

- $TC:O(N)$
- $SC:O(N)$
- 基于queue的BFS，只需要将current node的next指向queue中保存的第一个node即可，但是不是constant extra space

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return null;
        
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        // 压入null来标记一层的结束，同时也免去了对每一层最后一个node.next的特殊处理
        // 如果queue不允许保存null，那么就像一般的BFS一样用size来控制每层的遍历
        queue.add(null);
        
        while (!queue.isEmpty()) {
            Node node = queue.remove();
            if (node == null) {
                // 最后一个node会出现值为null且queue为空的情况
                // 因此不能用node == null && queue.size() > 0
                if (queue.size() > 0) queue.add(null);
            } else {
                node.next = queue.peek();
                if (node.left != null) {
                    queue.add(node.left);
                    queue.add(node.right);
                }
            }
        }
        
        return root;
    }
}
```

## Solution 2: Recursive

- $TC:O(N)$
- $SC:O(logN)$
- 对每个结点node，查看node.left是否为null（即是否存在下一个level），若node.left不为null，则可以将node.left.next设置为node.right。
- 然后查看node.next是否为null，若不为null，则将node.right.next设置为node.next.left，最后对左右子树进行递归。

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return null;
        
        if (root.left != null) {
            root.left.next = root.right;
            if (root.next != null) root.right.next = root.next.left;
        }
        
        connect(root.left);
        connect(root.right);
        
        return root;
    }
}
```

## Solution 3: Iterative

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return null;
        
        // 将每一层的最左侧node设置为current level的初始结点
        // 然后在当前层构造下一层的next连接
        Node curLevelStart = root;
        while (curLevelStart != null) {
            // 从一层的最左侧开始遍历
            Node curNode = curLevelStart;
            
            while (curNode != null) {
                if (curNode.left != null) curNode.left.next = curNode.right;
                // 必须同时判断curNode的right和next均不为null
                // 因为最后一层存在有next没right的结点，造成NullPointerException
                if (curNode.right != null && curNode.next != null) 
                    curNode.right.next = curNode.next.left;

                // 利用在上一层构造好的next横跨遍历当前层
                curNode = curNode.next;
            }
            
            curLevelStart = curLevelStart.left;
        }
        
        return root;
    }
}
```

