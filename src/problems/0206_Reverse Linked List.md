# 206. Reverse Linked List (2021.02.11)

https://leetcode.com/problems/reverse-linked-list/

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

## Solution 1: Iterative I

- $TC:O(n)$
- $SC:O(1)$
- 创建一个dummy结点，然后从head开始依次将current结点插入dummy结点的next位置

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode dummy = new ListNode(-1), cur = head;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = dummy.next;
            dummy.next = cur;
            cur = next;
        }
        
        return dummy.next;
    }
}
```

## Solution 2: Iterative II

- $TC:O(n)$
- $SC:O(1)$
- 从head开始依次反转结点，例如从1 -> 2 -> 3 -> null变成null <- 1 <- 2 3 -> null，结点2和3之间的连接会被破坏

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode prev = null, cur = head;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        
        return prev;
    }
}
```

## Solution 3: Recursive

- $TC:O(n)$
- $SC:O(1)$
- 一路递归调用下去直到最后一个结点，然后通过head.next.next = head将下一个结点的next指向当前结点，同时返回最后一个结点作为反转链表的新的头结点
- 其实不返回node整个链表也会被反转，精髓就在于head.next.next = head。

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode node = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        
        return node;
    }
}
```