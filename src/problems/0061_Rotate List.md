## 61. Rotate List (2020.08.06)

https://leetcode.com/problems/rotate-list/


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

## Solution 1

- $TC:O(N)$
- $SC:O(1)$
- First iteration to count the length of the list, (k % len) represents the number of nodes need to be rotated (as k might larger than length)
- So we can move the latter part to the front

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null) return head;
        
        int length = 1;
        ListNode tmp = head;
        while (tmp.next != null) {
            tmp = tmp.next;
            length++;
        }
        tmp.next = head; // create a circle
        
        // tmp.next is referring to head now, unnecessary to do tmp = head
        for (int i = 0; i < length - k % length; i++) {
            tmp = tmp.next;
        }
        head = tmp.next;
        tmp.next = null;
        return head;
    }
}
```

