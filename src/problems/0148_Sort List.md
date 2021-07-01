# 148. Sort List (2021.01.17)

https://leetcode.com/problems/sort-list/

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
```

## Solution 1: Top-down Merge Sort

- $TC:O(nlogn)$
- $SC:O(logn)$
- 由于快排需要随机打乱，否则时间复杂度容易到达O(n^2)，对于链表来说不是一个好的选择。因此更适合使用归并排序来进行链表的排序。

```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode prev = null, slow = head, fast = head;
        // 在结点数为偶数时，slow会停留在右半侧的初始点（比如4个node的情况下，会停在第三个）
        // 这样会使得划分不是那么均匀，因此这里使用了一个prev来标记slow的前一个结点
        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        
        prev.next = null;
        
        ListNode l1 = sortList(head);
        ListNode l2 = sortList(slow);
        return merge(l1, l2);
    }
    
    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(-1), cur = dummyHead;
        
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            
            cur = cur.next;
        }
        
        cur.next = (l1 == null) ? l2 : l1;
        
        return dummyHead.next;
    }
}
```

## Solution 2: Bottom-up Merge Sort
- $TC:O(nlogn)$
- $SC:O(1)$
- Bottom-up的归并排序可以将空间复杂度优化至O(1)，但是相比于Top-down的版本，实现起来要复杂很多，因此在空间不紧张的情况下推荐使用Top-down。

```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode cur = head;
        int len = 0;
        while (cur != null) {
            cur = cur.next;
            len++;
        }
        
        ListNode dummyHead = new ListNode(-1), tail, left, right;
        dummyHead.next = head;
        for (int i = 1; i < len; i *= 2) {
            cur = dummyHead.next;
            tail = dummyHead;
            while (cur != null) {
                left = cur;
                right = split(left, i);
                cur = split(right, i);
                tail = merge(left, right, tail);
            }
        }
        
        return dummyHead.next;
    }
    
    private ListNode split(ListNode l, int step) {
        ListNode prev = null;
        for (int i = 0; l != null && i < step; i++) {
            prev = l;
            l = l.next;
        }
        
        if (l == null) return null;
        prev.next = null;
        return l;
    }
    
    private ListNode merge(ListNode l1, ListNode l2, ListNode cur) {
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            
            cur = cur.next;
        }
        
        cur.next = (l1 == null) ? l2 : l1;
        
        while (cur.next != null) cur = cur.next;
        return cur;
    }
}
```