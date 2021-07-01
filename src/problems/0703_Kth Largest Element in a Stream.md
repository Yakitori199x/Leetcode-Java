# 703. Kth Largest Element in a Stream (2021.01.23)

https://leetcode.com/problems/kth-largest-element-in-a-stream/

```java
/**
 * Your KthLargest object will be instantiated and called as such:
 * KthLargest obj = new KthLargest(k, nums);
 * int param_1 = obj.add(val);
 */
```

## Solution 1: Min heap (Priority queue)

- $TC:O(nlogk)$
- $SC:O(logk)$
- 要求的是第k大的数，那么只需要维护一个大小为k的最小堆，最小堆中的元素就是当前时刻list中最大的k个元素，头部元素即为kth largest。
- 这类问题也可以使用partition算法来解决，但由于本题背景是stream（不断有新的元素被加入），使用partition的时间复杂度会到达O(n^2)。

```java
class KthLargest {
    
    private int k;
    private Queue<Integer> pq;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.pq = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        /*
         * 另一种能够稍微提升效率的方法：
         * 当pq.size() < k时，直接加入pq，否则判断pq.peek()是否小于val
         * 若pq.peek()小于val，则需要将peek移除再把val加pq，也就是说不需要每次都默认加入再移除
         */
        pq.offer(val);
        if (pq.size() > k) {
            pq.poll();
        }
        return pq.peek();
    }
}
```

## Solution 2: Binary Search Tree

- $TC:O(nh)$, h的值取决于形成的BST，最好可以是完全二叉树$O(logn)$，最差可以是一字长蛇阵$O(n)$
- $SC:O(n)$
- 效率不高，思路挺有意思，树的每个结点记录了以自己为root的树的结点数，根据这个来判断每个结点所处的位置。

```java
class KthLargest {

    public class TreeNode {
        int val;
        int count;
        TreeNode left, right;
        
        TreeNode(int val) {
            this.val = val;
            this.count = 1;
        }
    }
    
    private int k;
    private TreeNode root;
    
    public KthLargest(int k, int[] nums) {
        this.k = k;
        for (int num : nums) add(num);
    }
    
    public int add(int val) {
        root = add(root, val);
        return getKthLargest(root, k);
    }
    
    private TreeNode add(TreeNode root, int val) {
        if (root == null) return new TreeNode(val);
        
        root.count++;
        if (val >= root.val) root.right = add(root.right, val);
        else root.left = add(root.left, val);
        
        return root;
    }
    
    private int getKthLargest(TreeNode root, int k) {
        if (root == null || root.count < k) return -1;
        
        int res = 0;
        int pos = 1 + (root.right == null ? 0 : root.right.count);
            
        if (pos == k) return root.val;
        else if (pos > k) res = getKthLargest(root.right, k);
        else res = getKthLargest(root.left, k -pos);
        
        return res;
    }
}
```