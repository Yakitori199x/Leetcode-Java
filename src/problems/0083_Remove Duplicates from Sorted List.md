## 83. Remove Duplicates from Sorted List (2020.10.17)

https://leetcode.com/problems/remove-duplicates-from-sorted-list/


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

## Solution 1: Iteration

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        
        ListNode current = head;
        while (current != null && current.next != null) {
            if (current.val == current.next.val) {
                current.next = current.next.next;
            } else {
                current = current.next;
            }
        }
        
        return head;
    }
}
```

## Solution 2: Recursion

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) return head;
        
        head.next = deleteDuplicates(head.next);
        return head.val == head.next.val ? head.next : head;
    }
}
```

