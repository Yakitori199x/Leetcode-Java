## 86. Partition List (2020.10.03)

https://leetcode.com/problems/partition-list/


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

## Solution 1: Two pointers

- $TC:O(N)$
- $SC:O(1)$
- 遍历链表，如果当前node的值小于x，则加入before，否则加入after

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null) return null;
        
        // 设置dummy node，可以省去处理很多corner cases
        // 比如最初的node的创建，或是遍历后没有任何加入的node的情况的处理
        ListNode beforeHead = new ListNode(-1), afterHead = new ListNode(-1);
        ListNode before = beforeHead, after = afterHead;
        
        while (head != null) {
            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                after.next = head;
                after = after.next;
            }
            
            head = head.next;
        }
        
        // 记得将after.next设置为null，否则会形成cycle
        after.next = null;
        before.next = afterHead.next;
        return beforeHead.next;
    }
}
```

