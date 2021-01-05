## 21. Merge Two Sorted Lists (2020.04.08)

https://leetcode.com/problems/merge-two-sorted-lists/


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

## Solution 1: By recursion

- $TC:O(m+n)$
- $SC:O(m+n)$

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        
        if (l1.val <= l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);;
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

## Solution 2: By iteration ver1.

- $TC:O(m+n)$
- $SC:O(m+n)$

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        
        while (l1 != null || l2 != null) {
            int val1 = l1 == null ? Integer.MAX_VALUE : l1.val;
            int val2 = l2 == null ? Integer.MAX_VALUE : l2.val;
            
            if (val1 <= val2) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            
            cur = cur.next;
        }
        
        return dummy.next;
    }
}
```

## Solution 3: By iteration ver2.

- $TC:O(m+n)$
- $SC:O(m+n)$

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            
            cur = cur.next;
        }
        
        cur.next = l1 == null ? l2 : l1;
        
        return dummy.next;
    }
}
```

