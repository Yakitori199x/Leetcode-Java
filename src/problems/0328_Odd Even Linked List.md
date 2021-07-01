# 328. Odd Even Linked List (2021.03.04)

https://leetcode.com/problems/odd-even-linked-list/

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
- 遍历链表去构造odd和even两个链表，最后将odd的末结点和even的头结点连接起来。

```java
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head == null) return null;
        
        ListNode oddHead = new ListNode(-1), evenHead = new ListNode(-1);
        ListNode curOdd = head, curEven = head.next;
        oddHead.next = head;
        evenHead.next = head.next;
        
        // 根据even的当前结点来判断
        // 当curEven为最后一个结点（链表结点数为偶数的情况）
        // 或是curOdd为最后一个结点，curEven为null（链表结点数为奇数的情况）
        // 就可以结束循环，防止下面的curOdd.next = evenHead.next出现NPE
        while (curEven != null && curEven.next != null) {
            curOdd.next = curEven.next;
            curOdd = curOdd.next;
            curEven.next = curOdd.next;
            curEven = curEven.next;
        }
        
        curOdd.next = evenHead.next;
        return oddHead.next;
    }
}
```