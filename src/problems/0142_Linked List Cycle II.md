## 142. Linked List Cycle II (2020.08.05)

https://leetcode.com/problems/linked-list-cycle-ii/


```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
```

## Solution 1: Based on Floyd Cycle Detection algorithm (Fast-Slow pointers)

- $TC:O(N)$
- $SC:O(1)$
- The first pass is to check if the cycle exists
- If it is, we need to find the begin node of the cycle in the next step, otherwise, return null
- To find the begin node, we can define x as distance from head to the begin node, y as distance from the begin node to the meeting node, z as distance from the meeting node to the begin node
- As fast moves 2 times as slow, so 2 _ (x + y) = x + 2 _ y + z -> x = z
- (Just a very simple case, because fast might have iterated the cycle many times -> x + y + t * (y + z))
- So we can set one of them to the head, and move two pointers in the same pace until they meet again
- The new meeting node is the begin node of the cycle
- For more detailed explanation, please refer to: https://leetcode.com/problems/linked-list-cycle-ii/discuss/44781/Concise-O(n)-solution-by-using-C%2B%2B-with-Detailed-Alogrithm-Description

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (fast == slow) {
                fast = head;
                while (slow != fast) {
                    slow = slow.next;
                    fast = fast.next;
                }
                return fast;
            }
        }
        
        return null;
    }
}
```

