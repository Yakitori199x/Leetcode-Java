# 24. Swap Nodes in Pairs (2021.03.04)

https://leetcode.com/problems/swap-nodes-in-pairs/

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
- 维护三个指针，分别是当前待交换的结点对first，second，以及first结点的前置结点prev（用dummyHead来处理head这个corner case）
- 之后就是交换first和second的位置，然后把prev更新到交换后first再次进行同样的操作。

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(-1), prev = dummy;
        dummy.next = head;
        
        // 这里可以保证prev自身不会为null，所以不必检查prev != null
        while (prev.next != null && prev.next.next != null) {
            ListNode first = prev.next, second = prev.next.next;
            first.next = second.next;
            second.next = first;
            prev.next = second;
            prev = first;
        }
        
        return dummy.next;
    }
}
```

## Solution 2: Recursive

- $TC:O(n)$
- $SC:O(n)$
- 对整个链表交换pairs，就相当于先对除了头两个结点外的链表剩余部分交换pairs。
- 然后交换头两个结点的位置，以此类推进行递归。

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode first = head, second = head.next;
        first.next = swapPairs(head.next.next);
        second.next = first;
        return second;
    }
}
```