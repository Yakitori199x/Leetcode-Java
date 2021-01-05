## 108. Convert Sorted Array to Binary Search Tree (2020.08.22)

https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/


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

想要从一个有序数列构建height-balanced的BST，只需要不断以nums[mid]为根，数列左半部分作为左子树，右半部分作为右子树的方式划分数组即可。首先这样可以保证右左子树的结点数平衡，其次是该操作不断作用于左右子树，最终可以确保高度相差不超过1。

## Solution 1: Recursion

- $TC:O(N)$
- $SC:O(h)$
- 找出中点作为根结点，然后对左右子树进行同样的操作，直到左边界大于右边界。

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return buildTree(nums, 0, nums.length - 1);
    }
    
    private TreeNode buildTree(int[] nums, int i, int j) {
        if (j < i) return null;
        
        int mid = (j - i) / 2 + i;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = buildTree(nums, i, mid - 1);
        root.right = buildTree(nums, mid + 1, j);
        return root;
    }
}
```

## Solution 2: DFS (Iterative), inorder-like

- $TC:O(N)$
- $SC:O(h)$
- 类中序遍历的iterative方法，从根结点开始，不断缩小范围构建左结点并入栈，直到左侧不再存在可用结点，再将栈顶元素推出，构建其右结点。

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums == null || nums.length == 0) return null;
        
        Deque<MyNode> stack = new LinkedList<>();
        int l = 0, r = nums.length - 1, mid = (r - l) / 2 + l;
        TreeNode root = new TreeNode(nums[mid]);
        stack.push(new MyNode(root, 0, nums.length - 1));
        
        TreeNode temp = root;
        while (!stack.isEmpty()) {
            while (l <= mid - 1) {
                r = mid - 1;
                mid = (r - l) / 2 + l;
                temp.left = new TreeNode(nums[mid]);
                stack.push(new MyNode(temp.left, l, r));
                temp = temp.left;
            }
            
            MyNode curNode = stack.pop();
            temp = curNode.node;
            l = curNode.leftIndex;
            r = curNode.rightIndex;
            mid = (r - l) / 2 + l;
            if (r >= mid + 1) {
                l = mid + 1;
                mid = (r - l) / 2 + l;
                temp.right = new TreeNode(nums[mid]);
                stack.push(new MyNode(temp.right, l, r));
                temp = temp.right;
            }
        }
        
        return root;
    }
    
    private class MyNode {
        public TreeNode node;
        public int leftIndex;
        public int rightIndex;
        
        MyNode(TreeNode node, int leftIndex, int rightIndex) {
            this.node = node;
            this.leftIndex = leftIndex;
            this.rightIndex = rightIndex;
        }
    }
}
```

## Solution 3: DFS (Iterative), preorder-like

- $TC:O(N)$
- $SC:O(h)$
- 类前序遍历的iterative方法，先构建一个placeholder结点（root），入栈。以此作为初始状态，当栈非空时，推出栈顶元素，找到nums的中点并赋值给placeholder结点（正式创建成功）。然后再检查其是否有左右子树，若存在，这分别构建placeholder结点并入栈，直到全部结点都被遍历。

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums == null || nums.length == 0) return null;
        
        Deque<MyNode> stack = new LinkedList<>();
        TreeNode root = new TreeNode(-1);
        stack.push(new MyNode(root, 0, nums.length - 1));
        
        while (!stack.isEmpty()) {
            MyNode curNode = stack.pop();
            TreeNode temp = curNode.node;
            int l = curNode.leftIndex, r = curNode.rightIndex, mid = (r - l) / 2 + l;
            temp.val = nums[mid];
            
            if (l <= mid - 1) {
                temp.left = new TreeNode(-1);
                stack.push(new MyNode(temp.left, l, mid - 1));
            }
            
            if (r >= mid + 1) {
                temp.right = new TreeNode(-1);
                stack.push(new MyNode(temp.right, mid + 1, r));
            }
        }
        
        return root;
    }
    
    private class MyNode {
        public TreeNode node;
        public int leftIndex;
        public int rightIndex;
        
        MyNode(TreeNode node, int leftIndex, int rightIndex) {
            this.node = node;
            this.leftIndex = leftIndex;
            this.rightIndex = rightIndex;
        }
    }
}
```

## Solution 4: BFS

- $TC:O(N)$
- $SC:O(N)$
- 类似Solution 3，先创建一个根结点的placeholder，然后以此为初始状态开始BFS。（placeholder可以简化代码，否则在循环内外都需要进行赋值，且当前结点的左右结点的计算还要保存两套left，right和mid。）

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums == null || nums.length == 0) return null;
        
        Queue<MyNode> queue = new LinkedList<>();
        TreeNode root = new TreeNode(-1);
        queue.offer(new MyNode(root, 0, nums.length - 1));
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                MyNode curNode = queue.poll();
                TreeNode temp = curNode.node;
                int l = curNode.leftIndex, r = curNode.rightIndex, mid = (r - l) / 2 + l;
                temp.val = nums[mid];
                
                if (l <= mid - 1) {
                    temp.left = new TreeNode(-1);
                    queue.offer(new MyNode(temp.left, l, mid - 1));
                }
                
                if (r >= mid + 1) {
                    temp.right = new TreeNode(-1);
                    queue.offer(new MyNode(temp.right, mid + 1, r));
                }
            }
        }
        
        return root;
    }
    
    private class MyNode {
        public TreeNode node;
        public int leftIndex;
        public int rightIndex;
        
        MyNode(TreeNode node, int leftIndex, int rightIndex) {
            this.node = node;
            this.leftIndex = leftIndex;
            this.rightIndex = rightIndex;
        }
    }
}
```

