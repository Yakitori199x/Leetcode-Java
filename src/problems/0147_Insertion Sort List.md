# 147. Insertion Sort List (2021.01.17)

https://leetcode.com/problems/insertion-sort-list/

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

## Solution 1: Negate the value in array

- $TC:O(n^2)$
- $SC:O(1)$
- 链表版本的插入排序，当已排序的序列的最后一个结点的值(cur)小于下一个待排序结点的值时，可以直接跳过插入过程。

```java
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode dummy = new ListNode(-1), cur = head, pre = dummy;
        dummy.next = head;
        
        while (cur != null) {
            if (cur.next != null && cur.val > cur.next.val) {
                ListNode temp = cur.next;
                while (pre.next != null && pre.next.val < temp.val) {
                    pre = pre.next;
                }
            
                cur.next = temp.next;
                temp.next = pre.next;
                pre.next = temp;
                pre = dummy;
            } else {
                cur = cur.next;
            }
        }
        
        return dummy.next;
    }
}
```