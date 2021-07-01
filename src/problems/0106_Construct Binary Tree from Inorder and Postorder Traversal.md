# 106. Construct Binary Tree from Inorder and Postorder Traversal (2021.02.28)

https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

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

## Solution 1: Recursion

- $TC:O(N^2)$，每次递归会构造一个结点，但是寻找root在inorder中的位置需要每一层消耗O(N)，准确一点就是O(N*h)
- $SC:O(h)$
- 本题的关键在于，后序遍历的最末结点就是root，而中序遍历root刚好处于一个把左右子树分开的位置（即root左侧为左子树的中序遍历，右侧同理）。
- 因此，首先我们可以从postorder获取到root，然后搜索其在inorder中的位置。
- 如此一来，我们就可以通过inorder中的root获取到左右子树的中序遍历结果，并且根据结点的数量同时可以分割出左右子树的后序遍历结果。
- 有了这些结果，就可以很容易地通过递归来实现，即对左右子树的中序，后续遍历的结果调用buildTree即可。
- 从语义上来讲buildTree(inorder, is, ie, postorder, ps, pe)就是基于整棵树的中序遍历和后序遍历构建整棵树。
- 那么在buildTree的过程中构造出root后，对buildTree的递归调用就分别是基于左子树的中序遍历和后序遍历构建左子树，以及基于右子树的中序遍历和后序遍历构建右子树。
- 还是相当清晰易懂的。

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        return buildTree(inorder, 0, inorder.length - 1, postorder, 0, postorder.length - 1);
    }
    
    private TreeNode buildTree(int[] inorder, int iStart, int iEnd, int[] postorder, int pStart, int pEnd) {
        if (iStart > iEnd || pStart > pEnd) return null;
        
        // 后序遍历的最后一个值即为根节点的值
        // 用pos来记录root在inorder中的位置，从而确定左右子树
        int rootVal = postorder[pEnd], pos = 0;
        for (int i = iStart; i <= iEnd; i++) {
            if (rootVal == inorder[i]) {
                pos = i;
                break;
            }
        }
        
        TreeNode root = new TreeNode(rootVal);
        // 只要pos的位置确定了，左右子树的inorder范围就直接可以确定
        // 对于左右子树的postorder，也可以根据左子树的结点数(pos-iStart)推算出对应的范围
        root.left = buildTree(inorder, iStart, pos - 1, postorder, pStart, pStart + pos - iStart - 1);
        root.right = buildTree(inorder, pos + 1, iEnd, postorder, pStart + pos - iStart, pEnd - 1);
        return root;
    }
}
```

## Solution 2: Recursion, optimized

- $TC:O(N)$
- $SC:O(N)$
- 可以发现，Solution1中对于求rootVal在inorder中的范围需要不小的开销，这一部分是可以进一步优化的。
- 方法就是通过一个map来记录inorder中各个值对应的位置，搜索的时候可以只消耗O(1)。

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(inorder, 0, inorder.length - 1, postorder, 0, postorder.length - 1, map);
    }
    
    private TreeNode buildTree(int[] inorder, 
                               int iStart, 
                               int iEnd, 
                               int[] postorder, 
                               int pStart, 
                               int pEnd, 
                               Map<Integer, Integer> map) {
        if (iStart > iEnd || pStart > pEnd) return null;
        
        int rootVal = postorder[pEnd], pos = map.get(rootVal);
        TreeNode root = new TreeNode(rootVal);
        root.left = buildTree(inorder, iStart, pos - 1, postorder, pStart, pStart + pos - iStart - 1, map);
        root.right = buildTree(inorder, pos + 1, iEnd, postorder, pStart + pos - iStart, pEnd - 1, map);
        return root;
    }
}
```

## Solution 3: Iterative

- $TC:O(N)$
- $SC:O(N)$
- Iterative解法其实是根据postorder，从root开始一路往右构造结点，直到遇到rightmost结点（即inorder[iEnd]）。
- 同时在遍历的过程中要把遍历过的结点的path记录在stack里，因为后续可能要用来构造其左子树。
- 当遇到rightmost结点后，开始将stack中的结点出栈，如果该结点没有左子树则会被直接略过。
- 如果该结点有左子树，则出栈操作会被暂停，因为stack.peek().val != inorder[iEnd]。
- 比如结点5有左子树而结点4没有左子树，则其后序和中序遍历为post:[...,3,4,5,6]，in:[...,6,...,5,4,3]。
- 可以发现在5被弹出后inorder会进入到其左子树的范围。
- 之后就是构建起左子树的root并进行跟之前同样的操作即可。

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        if (inorder == null || postorder == null) return null;
        
        int iEnd = inorder.length - 1, pEnd = postorder.length - 1;
        TreeNode root = new TreeNode(postorder[pEnd--]), prev = null;
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        
        while (pEnd >= 0) {
            // 遇到rightmost结点，开始出栈，并且把出栈的结点保存到prev
            // 之后会构造prev的左子树（如果有的话）
            while (!stack.isEmpty() && stack.peek().val == inorder[iEnd]) {
                prev = stack.pop();
                iEnd--;
            }
            
            TreeNode cur = new TreeNode(postorder[pEnd--]);
            // prev为null，说明还可以继续往右构造
            // 否则，cur就是prev的左子树的根结点
            if (prev != null) {
                prev.left = cur;
                prev = null;
            } else {
                TreeNode curTop = stack.peek();
                curTop.right = cur;
            }
            stack.push(cur);
        }
        
        return root;
    }
}
```