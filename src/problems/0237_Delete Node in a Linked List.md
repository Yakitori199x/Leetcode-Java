# 237. Delete Node in a Linked List (2021.03.09)

https://leetcode.com/problems/delete-node-in-a-linked-list/

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
```

## Solution 1

- $TC:O(1)$
- $SC:O(1)$
- 因为不给链表的head，而是给你待删除的结点。
- 那就直接把next结点的值赋给node，然后删除next结点。

```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```