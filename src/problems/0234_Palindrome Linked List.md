## 234. Palindrome Linked List (2020.07.26)

https://leetcode.com/problems/palindrome-linked-list/


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

## Solution 1: Slow-Fast Pointers

- $TC:O(n)$
- $SC:O(1)$
- Create two pointers and iterate the list, the slow one moves 1 step each time, the fast one moves 2 steps each time, when fast meets the end of the list, slow stops in the middle and splits the list to two halves, reverse the right half and compare each node (left -> right, right -> left)

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode slow = head, fast = head;
        
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        
        // it means the list has odd number of nodes
        // move one step forward to make right half smaller
        if (fast != null) slow = slow.next;
        
        slow = reverse(slow);
        fast = head;
        
        while (slow != null) {
            if (slow.val != fast.val) return false;
            slow = slow.next;
            fast = fast.next;
        }
        
        return true;
    }
    
    private ListNode reverse (ListNode cur) {
        ListNode prev = null;
        
        while (cur != null) {
            ListNode tmp = cur.next;
            cur.next = prev;
            prev = cur;
            cur = tmp;
        }
        
        return prev;
    }
}
```

## Solution 2: Recursion

- $TC:O(n)$
- $SC:O(n)$, cost of recursion stack
- Need to keep a field of ListNode so the change can reflect to each recursion step

```java
class Solution {
    private ListNode tmp;
        
    public boolean isPalindrome(ListNode head) {
        tmp = head;
        return check(head);
    }
    
    private boolean check(ListNode head) {
        if (head == null) return true;
        boolean isQualified = check(head.next) && (head.val == tmp.val);
        tmp = tmp.next;
        return isQualified; 
    }
}
```

