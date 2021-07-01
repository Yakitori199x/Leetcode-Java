# 725. Split Linked List in Parts (2021.03.05)

https://leetcode.com/problems/split-linked-list-in-parts/

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

## Solution 1: Iterative, in-place

- $TC:O(n)$
- $SC:O(1)$
- 首先遍历链表获取链表的长度，然后就可以知道将该链表按本题要求分为k份，每部分需要多少个结点。
- 之后就是再次遍历链表，并根据每部分的长度去按顺序切分链表即可。

```java
class Solution {
    public ListNode[] splitListToParts(ListNode root, int k) {
        int len = 0;
        for (ListNode cur = root; cur != null; cur = cur.next) len++;
        
        // n表示一般情况下每部分的结点数
        // 但是有可能出现无法整除的情况，即前面的一些parts包含的结点数会比n多1
        // 那么这类parts的个数就是len % k，即k部分中有r个包含的结点为n+1
        int n = len / k, r = len % k;
        ListNode[] res = new ListNode[k];
        ListNode node = root, prev = null;

        // 因为res里可能会有类似[[1],[2],[3],[],[]]的空集
        // 所以遍历node为null就可以收手了
        for (int i = 0; node != null && i < k; i++) {
            res[i] = node;
            for (int j = 0; j < n + (i < r ? 1 : 0); j++) {
                prev = node;
                node = node.next;
            }
            
            prev.next = null;
        }
        
        return res;
    }
}
```

## Solution 2: Iterative, new-list

- $TC:O(n)$
- $SC:O(n)$
- 不破坏原链表结构，而是将重新创建每个结点。

```java
class Solution {
    public ListNode[] splitListToParts(ListNode root, int k) {
        int len = 0;
        for (ListNode cur = root; cur != null; cur = cur.next) len++;
        
        int n = len / k, r = len % k;
        ListNode[] res = new ListNode[k];
        ListNode node = root;
        for (int i = 0; node != null && i < k; i++) {
            ListNode newHead = new ListNode(-1), cur = newHead;
            for (int j = 0; j < n + (i < r ? 1 : 0); j++) {
                cur.next = new ListNode(node.val);
                cur = cur.next;
                node = node.next;
            }
            
            res[i] = newHead.next;
        }
        
        return res;
    }
}
```