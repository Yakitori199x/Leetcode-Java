# 82. Remove Duplicates from Sorted List II (2021.03.04)

https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/

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

## Solution 1: Iterative

- $TC:O(n)$
- $SC:O(1)$
- 维护prev和cur两个pointers，从dummy和head开始遍历链表。
- 如果发现cur和cur.next的值相等，那么就持续向后移动cur直到这一串相等序列的最后一个结点。
- 然后将prev.next直接设置为cur.next，就可以移除一整串相等序列。


```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(-1), prev = dummy, cur = head;
        dummy.next = head;
        
        while (cur != null) {
            while (cur.next != null && cur.val == cur.next.val) cur = cur.next;
            
            // 通过prev.next是否为cur来判断是否存在duplicate序列
            if (prev.next == cur) {
                prev = cur;
            } else {
                prev.next = cur.next;
            }
            
            cur = cur.next;
        }
        
        return dummy.next;
    }
}
```

## Solution 2: Recursive

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) return head;
        
        if (head.val != head.next.val) {
            // 如果当前结点和next结点的值不相等
            // 则直接对next结点为首的链表进行递归
            head.next = deleteDuplicates(head.next);
        } else {
            // 如果当前结点和next结点的值相等
            // 则移动当前结点直到最后一个duplicate，然后再对next结点的链表递归
            // 而且需要把当前结点head设置为递归的返回值，即移除最后一个duplicate
            while (head.next != null && head.val == head.next.val) head = head.next;
            head = deleteDuplicates(head.next);
        }
        
        return head;
    }
}
```