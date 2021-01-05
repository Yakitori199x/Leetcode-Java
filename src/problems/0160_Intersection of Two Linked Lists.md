## 160. Intersection of Two Linked Lists (2020.10.20)

https://leetcode.com/problems/intersection-of-two-linked-lists/


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
```

## Solution 1: Hash Table

- $TC:O(m+n)$
- $SC:O(m)$

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        Set<ListNode> set = new HashSet<>();
        ListNode tmpA = headA, tmpB = headB;
        
        while (tmpA != null) {
            set.add(tmpA);
            tmpA = tmpA.next;
        }
        
        while (tmpB != null) {
            if (set.contains(tmpB)) return tmpB;
            tmpB = tmpB.next;
        }
        
        return null;
    }
}
```

## Solution 2: Calculate length of list

- $TC:O(m+n)$
- $SC:O(1)$
- 先获取两个list的长度，然后把较长的list的reference往后移动两者长度之差步，接下来一边移动一边判断指向两个list的引用是否相同即可

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode tmpA = headA, tmpB = headB;
        int lenA = 0, lenB = 0;
        
        while (tmpA != null) {
            tmpA = tmpA.next;
            lenA++;
        }
        
        while (tmpB != null) {
            tmpB = tmpB.next;
            lenB++;
        }
        
        tmpA = headA;
        tmpB = headB;
        
        if (lenA > lenB) {
            for (int i = 0; i < lenA - lenB; i++) tmpA = tmpA.next;
        } else {
            for (int i = 0; i < lenB - lenA; i++) tmpB = tmpB.next;
        }
        
        while (tmpA != tmpB) {
            tmpA = tmpA.next;
            tmpB = tmpB.next;
        }
        
        return tmpA;
    }
}
```

## Solution 3: Concatenation / Move back to the head of another list

- $TC:O(m+n)$
- $SC:O(1)$
- 同时遍历两个list，当遍历到listA尽头时，将tmpA指向headB，对listB继续遍历。listB的情况同理。
- 假设两个list的重合部分长度为c，不重合部分长度分别为a和b，以这种方法遍历最终会是A:(a+c+b)，B:(b+c+a)。
- 说明若两个list是重合的，即c>0。那么两个reference将在遍历到intersection的初始结点相遇，否则会一直遍历到两者为null后结束。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode tmpA = headA, tmpB = headB;
        
        while (tmpA != tmpB) {
            // 判断tmp==null而不是tmp.next==null，因为后者会陷入死循环
            tmpA = (tmpA == null) ? headB : tmpA.next;
            tmpB = (tmpB == null) ? headA : tmpB.next;
        }
        
        return tmpA;
    }
}
```

