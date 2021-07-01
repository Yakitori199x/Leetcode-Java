# 203. Remove Linked List Elements (2021.03.05)

https://leetcode.com/problems/remove-linked-list-elements/

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

## Solution 1: Iterative, 2 pointers

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(-1), prev = dummy, cur = head;
        dummy.next = head;
        
        while (cur != null) {
            if (cur.val == val) {
                prev.next = cur.next;
            } else {
                prev = prev.next;
            }
            
            cur = cur.next;
        }
        
        return dummy.next;
    }
}
```

## Solution 2: Iterative, 1 pointer

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(-1), cur = dummy;
        dummy.next = head;
        
        while (cur != null && cur.next != null) {
            if (cur.next.val == val) {
                cur.next = cur.next.next;
            } else {
                cur = cur.next;
            }
        }
        
        return dummy.next;
    }
}
```

## Solution 3: Recursion

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if (head == null) return head;
        
        head.next = removeElements(head.next, val);
        if (head.val == val) return head.next;
        return head;
    }
}
```