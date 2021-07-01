# 876. Middle of the Linked List (2021.01.23)

https://leetcode.com/problems/middle-of-the-linked-list/

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

## Solution 1: Fast-slow pointers

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null) return head;
        
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```