## 141. Linked List Cycle (2020.07.02)

https://leetcode.com/problems/linked-list-cycle/


```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
```

## Solution 1

- $TC:O(N)$, N is the number of nodes in the list
- $SC:O(N)$
- Iterate the list and store the visited node into a hash table
- check whether the current node has been visited, if not, move to next node

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        Set<ListNode> nodes = new HashSet<>();
        ListNode current = head;
        
        while (current != null) {
            if (nodes.contains(current)) return true;
            nodes.add(current);
            current = current.next;
        }
        
        return false;
    }
}
```

## Solution 2: Two pointers -> slow & fast

- $TC:O(N)$, N is the number of nodes in the list
- $SC:O(1)$
- fast pointer moves 2 steps a time, slow pointer moves 1 step a time
- if there is a cycle, fast pointer will meet slow pointer
- 若不存在环，则快指针可以在N/2次循环后到达链表尾部
- 若存在环，则假设在第p个节点出现环，且环的长度为q。
- 最差的情况为慢指针刚进入环时（p），快指针刚好在其下一个结点处（p.next），即快指针到慢指针之间有q-1步
- 因为快指针每次移动两步，慢指针每次移动一步，说明每次循环结束快指针可以缩短1步与慢指针的距离
- 最终可以在q-1次循环后追及慢指针 -> 则循环次数为 (p-1) + (q-1) = N-2 -> O(N)

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;
        
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (slow == fast) return true;
        }
        
        return false;
    }
}
```


```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;
        
        ListNode slow = head;
        ListNode fast = head.next;
        while (slow != fast) {
            if (fast.next == null || fast.next.next == null) return false;
            fast = fast.next.next;
            slow = slow.next;
        }
        
        return true;
    }
}
```

## Solution 3: 比较邪门的解法，会破坏链表结构

- $TC:O(N), N is the number of nodes in the list
- $SC:O(1)$
- 遍历链表，将每次遍历到的结点的next都指向head
- 因此只要检查当前结点的next是否指向head，是则说明可能该结点本身具有到head的环
- 或者可能是该结点为之前遍历到的结点，其next已经被修改

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode current = head;
        ListNode pre = head;
        
        while (current != null) {
            if (current.next == head) return true;
            current = current.next;
            pre.next = head;
            pre = current;
        }
        
        return false;
    }
}
```

