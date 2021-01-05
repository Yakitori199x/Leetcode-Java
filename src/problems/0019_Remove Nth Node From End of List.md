## 19. Remove Nth Node From End of List (2020.07.06)

https://leetcode.com/problems/remove-nth-node-from-end-of-list/


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

## Solution 1: Two pass

- $TC:O(L)$, L is the length of list -> (L + L - n)
- $SC:O(1)$
- First pass to get the length of the linked list, iterate L times
- After getting the length, it is easy to find n-th node from the end
- n-th node from the end equals to (L - n + 1)-th node from the front
- So we need to execute second pass to move (L - n - 1) steps to the previous node of the target
- However, there is the corner case as L = n, which means remove the head node
- It can be solved by setting up a dummy node and move (L - n) steps from the dummy node

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        int len = 0;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        
        ListNode tmp = head;
        while (tmp != null) {
            tmp = tmp.next;
            len++;
        }
        
        tmp = dummy;
        for (int i = 0; i < len - n; i++) {
            tmp = tmp.next;
        }
        tmp.next = tmp.next.next;
        
        return dummy.next;
    }
}
```

## Solution 2: One pass

- $TC:O(L)$, L is the length of list -> Loop for L times
- $SC:O(1)$
- Maintain two pointers -> left & right
- Keep the distance between left and right as n nodes -> Move right (n + 1) steps first
- Then move left and right at the same time until right meets the end
- In this situation, left will point to the previous node of target

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        
        ListNode left = dummy, right = dummy;
        for (int i = 0; i < n + 1; i++) right = right.next;
        
        while (right != null) {
            right = right.next;
            left = left.next;
        }
        
        left.next = left.next.next;
        return dummy.next;
    }
}
```

## Solution 3: Recursion

- $TC:O(L)$, L is the length of list
- $SC:O(1)$ or $O(L)$? -> Maybe there are L variables in recursion stack
- Return the index (count from the end), when the current index larger than n (which means it is closer to head than n-th node), assign the value of current node to previous node, finally return head.next

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        helper(head, n);
        return head.next;
    }
    
    private int helper(ListNode node, int n) {
        if (node == null) return 0;
        int index = helper(node.next, n) + 1;
        if (index > n) node.next.val = node.val;
        return index;
    }
}
```

