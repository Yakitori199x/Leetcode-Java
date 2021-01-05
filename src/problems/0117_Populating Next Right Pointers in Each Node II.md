## 117. Populating Next Right Pointers in Each Node II (2020.10.08)

https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/


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

## Solution 1: Level-order Traversal (Iterative)

- $TC:O(N)$
- $SC:O(1)$
- 本题和116题类似，差别在于二叉树不一定是完全二叉树，即next结点和current结点不一定相邻。
- 因此，我们不能只靠当前层的结点来构建下一层结点的next关系，需要同时为下一层维护一个Sentinal结点，始终指向下一层的第一个结点（curLevelStart = nextLevelSentinal.next）。
- 同时，还要为下一层维护一个遍历结点nextLevelMove，一边构建next一边移动。

```java
class Solution {
    public Node connect(Node root) {
        Node curLevelStart = root, nextLevelSentinal = new Node(-1);
        
        // 外层循环，纵向移动
        while (curLevelStart != null) {
            Node curLevelMove = curLevelStart, nextLevelMove = nextLevelSentinal;

            // 内层循环，横向移动
            while (curLevelMove != null) {
                if (curLevelMove.left != null) {
                    nextLevelMove.next = curLevelMove.left;
                    nextLevelMove = nextLevelMove.next;
                }
                
                if (curLevelMove.right != null) {
                    nextLevelMove.next = curLevelMove.right;
                    nextLevelMove = nextLevelMove.next;
                }
                
                curLevelMove = curLevelMove.next;
            }
            curLevelStart = nextLevelSentinal.next;
            // 记得置空，否则最后一层永远无法结束
            nextLevelSentinal.next = null;
        }
        
        return root;
    }
}
```

## Solution 2: Recursion

- $TC:O(N)$
- $SC:O(N)$, DFS的递归栈为O(logN)，但是findNext的递归栈最差可以达到O(N)
- 在进行DFS的同时，用findNext进行同一层的水平递归，找到next结点

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return null;
        
        if (root.left != null) {
            if (root.right != null) {
                root.left.next = root.right;
            } else {
                root.left.next = findNext(root.next);
            }
        }
        
        if (root.right != null) {
            root.right.next = findNext(root.next);
        }
        
        // 先对右子树递归，从右往左构建next关系，如果先递归左子树则可能出现当前层next关系没有构建全的情况
        connect(root.right);
        connect(root.left);
        return root;
    }
    
    private Node findNext(Node node) {
        if (node == null) return null;
        if (node.left != null) return node.left;
        if (node.right != null) return node.right;
        
        return findNext(node.next);
    }
}
```

