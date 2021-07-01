# 25. Reverse Nodes in k-Group (2021.03.04)

https://leetcode.com/problems/reverse-nodes-in-k-group/

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

## Solution 1: Iterative, reverse 1 by 1

- $TC:O(n)$
- $SC:O(1)$
- 维护一个前置结点start并将当前结点cur设置为start的下一个结点。
- 开始遍历链表直到找到k个结点（即一个待翻转的group），然后对start到cur.next这部分链表进行翻转。
- 被翻转的部分其实是start.next到cur，但是需要start作为dummy结点，以及cur.next作为end结点指示循环的结束。
- 翻转的过程就是将当前结点的next指向prev，然后将当前结点移动至下一个结点（已用temp保存）。
- 最终将翻转后的部分拼接回去，返回翻转后链表的最后一个结点（即first）作为下一次翻转的start。

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        // 如果k=1，则无需翻转，直接返回原链表
        if (head == null || head.next == null || k == 1) return head;
        
        ListNode dummy = new ListNode(-1), start = dummy, cur = head;
        dummy.next = head;
        
        int count = 0;
        while (cur != null) {
            // 先计数再判断，否则可能会遇到第k个结点刚好是链表最后一个结点
            // 导致不翻转直接离开循环
            count++;
            if (count % k == 0) {
                start = reverse(start, cur.next);
                cur = start.next;
            } else {
                cur = cur.next;
            }
        }
        
        return dummy.next;
    }
    
    private ListNode reverse(ListNode start, ListNode end) {
        ListNode prev = start, first = start.next, cur = first;

        // end作为循环停止的信号
        while (cur != end) {
            // before: start -> a -> b -> c -> ... -> end -> ...
            // after: start <- a(prev) <- b(cur), c(temp) -> ... -> end -> ...
            ListNode temp = cur.next;
            cur.next = prev;
            prev = cur;
            cur = temp;
        }
        
        // first在翻转后变成这个部分的末尾结点
        // 同时也是下一部分k个结点的start
        first.next = end;
        start.next = prev;
        return first;
    }
}
```

## Solution 2: Iterative, insertion

- $TC:O(n)$
- $SC:O(1)$
- 在翻转时采用将cur结点直接链接到end的next，直到prev.next变为end。

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || head.next == null || k == 1) return head;
        
        ListNode dummy = new ListNode(-1), start = dummy, cur = head;
        dummy.next = head;
        
        int count = 0;
        while (cur != null) {
            count++;
            if (count % k == 0) {
                // 这种翻转模式下end为被翻转部分的最后一个结点
                start = reverse(start, cur);
                cur = start.next;
            } else {
                cur = cur.next;
            }
        }
        
        return dummy.next;
    }
    
    private ListNode reverse(ListNode start, ListNode end) {
        ListNode prev = start, first = start.next, cur;
        while (prev.next != end) {
            // before: prev -> a(cur, first) -> b -> c -> ... -> end -> ...
            // after: prev -> c -> ... -> end -> b -> a -> ...
            cur = prev.next;
            prev.next = cur.next;
            cur.next = end.next;
            end.next = cur;
        }
        return first;
    }
}
```

## Solution 3: Recursion

- $TC:O(n)$
- $SC:O(n)$
- 先移动end找到下一组的k个结点的起始点（即第k+1个结点），同时计算经过的结点数。
- 如果经过的结点数不足k个，可以直接返回，不必翻转。
- 否则，先对end进行递归，并且设置end为翻转后的链表头结点。
- 之后就是翻转当前head到end这部分链表，最后返回翻转后的头结点去上一层递归。

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || k == 1) return head;
        
        int count = 0;
        ListNode end = head;
        while (end != null && count < k) {
            end = end.next;
            count++;
        }
        
        if (count == k) {
            end = reverseKGroup(end, k);
            while (count-- > 0) {
                ListNode temp = head.next;
                head.next = end;
                end = head;
                head = temp;
            }
            
            head = end;
        }
        
        return head;
    }
}
```