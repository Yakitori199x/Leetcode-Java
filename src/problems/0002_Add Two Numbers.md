## 2. Add Two Numbers (2020.05.16)

https://leetcode.com/problems/add-two-numbers/


```java
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

## Solution 1: Iteration

- $TC:O(max(m, n))$
- $SC:O(max(m, n))$
- Use dummy head node & ternary conditional operator to simplify the code.
- Use p, q to keep l1, l2 not be revised after execution

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode p = l1, q = l2, curr = head;
        int carry = 0;
        
        while (p != null || q != null) {
            int val1 = (p == null) ? 0 : p.val;
            int val2 = (q == null) ? 0 : q.val;
            int sum = val1 + val2 + carry;
            
            curr.next = new ListNode(sum % 10);
            curr = curr.next;
            carry = sum / 10;
            p = (p == null) ? p : p.next;
            q = (q == null) ? q : q.next;
        }
        
        if (carry > 0) curr.next = new ListNode(1);
        
        return head.next;
    }
}
```

## Solution 2: Recursion

- $TC:O(max(m, n))$
- $SC:O(max(m, n))$
- Use ternary conditional operator to simplify the code
- Use p, q to keep l1, l2 not be revised after execution

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        return addTwoNumbers(l1, l2, 0);
    }
    
    private ListNode addTwoNumbers(ListNode l1, ListNode l2, int carry) {
        if (l1 == null && l2 == null) {
            if (carry > 0) return new ListNode(1);
            return null;
        }
        
        int val1 = (l1 == null) ? 0 : l1.val;
        int val2 = (l2 == null) ? 0 : l2.val;
        int sum = val1 + val2 + carry;
        
        ListNode node = new ListNode(sum % 10);
        ListNode p = (l1 == null) ? l1 : l1.next;
        ListNode q = (l2 == null) ? l2 : l2.next;
        node.next = addTwoNumbers(p, q, sum / 10);
        return node;
    }
}
```

