## 109. Convert Sorted List to Binary Search Tree (2020.09.22)

https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */

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

## Solution 1: Recursive - ver 1

- $TC:O(NlogN)$, 乍看之下时间复杂度似乎是$$O(N^2)$$，但因为该题必须构造height balanced BST，保证了树的高度为logN，而且递归的时候并不是每个结点开销O(N)去寻找mid，而是同一层的所有结点开销总和为O(N)。（比如第二层的两个结点各开销N/2，第三层的四个结点各开销N/4...）
- $TC:O(logN)$
- 本题的核心思想是将list不断二分去构造一颗平衡的二叉树，和108题的思路是一样的，不过要找到链表的mid开销更大。
- 此处使用快慢指针去寻找中点并不断递归。

```java
class Solution {
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) return null;
        
        ListNode mid = findMid(head);
        TreeNode root = new TreeNode(mid.val);
        
        // base case，链表只剩余一个结点
        if (head == mid) return root;
        
        root.left = sortedListToBST(head);
        root.right = sortedListToBST(mid.next);
        
        return root;
    }
    
    private ListNode findMid(ListNode head) {
        ListNode prev = null, slow = head, fast = head;
        
        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // 将prev.next设置为null，使之后的递归能够在正确的范围内搜索mid
        // 但是会破坏原链表的结构
        // if是为了防止slow=head的情况
        if (prev != null) prev.next = null;
        
        return slow;
    }
}
```

## Solution 2: Recursive - ver 2

- $TC:O(NlogN)$
- $TC:O(logN)$
- 该解法为Solution 1的不破坏链表结构的解法，使用head和tail来界定接下来需要用的结点的范围

```java
class Solution {
    public TreeNode sortedListToBST(ListNode head) {   
        if (head == null) return null; 
        return buildBST(head, null);
    }
    
    private TreeNode buildBST(ListNode head, ListNode tail) {
        // 当上一次递归mid为tail之前的最后一个元素时
        // 调用buildBST(slow.next, tail)就会遇到head和tail相等的情况
        // 不排除该情况就会创建重复的结点
        if (head == tail) return null;
        
        ListNode slow = head, fast = head;
        while (fast != tail && fast.next != tail) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        TreeNode root = new TreeNode(slow.val);
        if (slow == head) return root;
        
        root.left = buildBST(head, slow);
        root.right = buildBST(slow.next, tail);
        
        return root;
    }
}
```

## Solution 3: convert to array + recursive

- $TC:O(N)$
- $SC:O(N)$
- 因为对链表查找mid的开销很大，所以该解法先把链表转换成arraylist再进行递归。
- 虽然节省了时间，但是需要维护一个含有N个元素的arraylist。属于time和space的tradeoff。

```java
class Solution {
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) return null;
        
        List<Integer> values = new ArrayList<>();
        ListNode temp = head;
        while (temp != null) {
            values.add(temp.val);
            temp = temp.next;
        }
        
        return sortedListToBST(values, 0, values.size());
    }
    
    private TreeNode sortedListToBST(List<Integer> values, int i, int j) {
        if (i >= j) return null;
        
        int mid = (j - i) / 2 + i;
        TreeNode root = new TreeNode(values.get(mid));
        
        root.left = sortedListToBST(values, i, mid);
        root.right = sortedListToBST(values, mid + 1, j);
        
        return root;
    }
}
```

## Solution 4: simulate inorder traversal

- $TC:O(N)$
- $TC:O(logN)$
- 因为本题给出的链表是排好序的，而我们知道二叉搜索树的中序遍历也是有序的，可以认为该链表对应着某个二叉搜索树的中序遍历。
- 同样地，我们可以通过模拟中序遍历的方式对链表进行递归。
- 此处我们只需要知道每个mid结点对应的index，而不需要真的去遍历搜索mid对应的结点，然后使用l和r去递归不断缩小范围。
- 在左侧递归结束后，我们可以利用ptr来创建当前的root，并在创建完后将ptr移动到下一个位置，这样保证了ptr正好就是该层递归的mid结点。

```java
class Solution {
    private ListNode ptr = null;
    
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) return null;
        
        int length = 0;
        ListNode temp = head;
        ptr = head;
        while (temp != null) {
            length++;
            temp = temp.next;
        }

        return buildBST(0, length - 1);
    }
    
    private TreeNode buildBST(int l, int r) {
        if (l > r) return null;
        
        int mid = (r - l) / 2 + l;
        TreeNode left = buildBST(l, mid - 1);
        
        TreeNode root = new TreeNode(ptr.val);
        ptr = ptr.next;
        root.left = left;
        
        root.right = buildBST(mid + 1, r);
        return root;
    }
}
```

