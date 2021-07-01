# 145. Binary Tree Postorder Traversal (2021.03.17)

https://leetcode.com/problems/binary-tree-postorder-traversal/

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
```

## Solution 1: Recursive

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        postorderTraversal(root, res);
        return res;
    }
    
    private void postorderTraversal(TreeNode root, List<Integer> res) {
        if (root == null) return;
        postorderTraversal(root.left, res);
        postorderTraversal(root.right, res);
        res.add(root.val);
    }
}
```

## Solution 2: Reverse Preorder

- $TC:O(n)$
- $SC:O(h)$
- 比较投机取巧的方法，如果只需要最终的后序遍历的结果那可以用这个实现。
- 但是如果需要遍历过程，比如按后序遍历的顺序一个一个输出，或者是某个场景在求根结点的解时需要先求得左右子树的解，那就要用Solution3和Solution4的实现。

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        if (root == null) return new LinkedList<>();
        
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode cur = root;
        stack.push(cur);
        while (!stack.isEmpty()) {
            cur = stack.pop();
            res.add(cur.val);
            if (cur.left != null) stack.push(cur.left);
            if (cur.right != null) stack.push(cur.right);
        }
        
        int i = 0, j = res.size() - 1;
        while (i < j) {
            int temp = res.get(i);
            res.set(i++, res.get(j));
            res.set(j--, temp);
        }
        return res;
    }
}
```

## Solution 3: Iterative 1

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        if (root == null) return new LinkedList<>();
        
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode cur = root;
        
        while (cur != null || !stack.isEmpty()) {
            // 优先遍历左子树，所以一路把cur向左往下遍历直到遇见没有左子树的结点
            while (cur != null) {
                stack.push(cur);
                cur = cur.left;
            }
            
            // 不能直接弹出，因为还不确定当前结点是否存在右子树
            cur = stack.peek();
            // 判断当前结点是否存在右子树
            if (cur.right != null) {
                // 有右子树，将cur移动到右子树的根结点
                // 为什么不需要判断其右子树是否被遍历过，关于这点会在下面解释
                cur = cur.right;
            } else {
                // 当前结点既没有左子树也没有右子树，可以弹出并且将结点的值加入到res
                cur = stack.pop();
                res.add(cur.val);
                // 这里需要检查cur是否是栈顶结点的右孩子
                // 而且要一路往上，边检查边弹出栈顶元素更新cur，并且将cur的值加入res
                // 直到stack为空（到达root）或是更新后的cur是栈顶元素的左孩子
                // 否则下一次循环时cur被设置为栈顶元素，又会重复进入右子树，即当前的cur
                while (!stack.isEmpty() && stack.peek().right == cur) {
                    cur = stack.pop();
                    res.add(cur.val);
                }
                // cur需要设置为null，否则会重复加入左子树的那段逻辑
                cur = null;
            }
        }
        
        return res;
    }
}
```

## Solution 4: Iterative 2

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        if (root == null) return new LinkedList<>();
        
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode cur = root, last = null;
        
        while (cur != null || !stack.isEmpty()) {
            // 相比于Solution3，while循环改成了if-else
            // 对应的操作还是不断把cur左移，优先遍历左子树
            if (cur != null) {
                stack.push(cur);
                cur = cur.left;
            } else {
                cur = stack.peek();
                // 当cur有右子树且右子树不等于上次遍历过的last结点时，设置cur为其右孩子
                if (cur.right != null && cur.right != last) {
                    cur = cur.right;
                } else {
                    cur = stack.pop();
                    res.add(cur.val);
                    // last会在叶子结点或者是左右子树都被遍历过的结点被设置为当前结点
                    // 之后就可以用于判断栈顶结点与当前遍历过的结点的关系（左子树？右子树？）
                    last = cur;
                    cur = null;
                }
            }
        }
        
        return res;
    }
}
```