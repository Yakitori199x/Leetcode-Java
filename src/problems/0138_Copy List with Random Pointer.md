## 138. Copy List with Random Pointer (2020.08.16)

https://leetcode.com/problems/copy-list-with-random-pointer/


```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
```

## Solution 1

- $TC:O(N)$
- $SC:O(N)$
- First pass: create copy node of each original node, and add them to the hashmap (original -> copy)
- Second pass: copy.next should refer to the copy node of original.next -> map.get(original).next = map.get(original.next). The same case for copy.random.

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        
        Map<Node, Node> map = new HashMap<>();
        Node tmp = head;
        
        while (tmp != null) {
            map.put(tmp, new Node(tmp.val));
            tmp = tmp.next;
        }
        
        tmp = head;
        while (tmp != null) {
            map.get(tmp).next = map.get(tmp.next);
            map.get(tmp).random = map.get(tmp.random);
            tmp = tmp.next;
        }
        
        return map.get(head);
    }
}
```

## Solution 2

- $TC:O(N)$
- $SC:O(1)$ if we don't consider the space for output
- See explanation in: https://leetcode.com/problems/copy-list-with-random-pointer/discuss/43491/A-solution-with-constant-space-complexity-O(1)-and-linear-time-complexity-O(N)
- (1) Associate copy node with original node in a single linked list (insert the copy node into the original node and its next node)
- (2) Create random link of copy nodes according to copy.random = original.random.next
- (3) Restore original linked list and extract the copy linked list

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        
        // Fisrt pass: Insert copy node
        Node tmp = head;
        while (tmp != null) {
            Node copy = new Node(tmp.val);
            copy.next = tmp.next;
            tmp.next = copy;
            tmp = tmp.next.next;
        }
        
        // Second pass: reate random link of copy nodes
        tmp = head;
        while (tmp != null) {
            Node copy = tmp.next;
            // Some nodes don't have random pointer
            if (tmp.random != null) copy.random = tmp.random.next;
            tmp = copy.next;
        }
        
        // Third pass: split two linked lists
        Node copyDummy = new Node(-1), copyTmp = copyDummy;
        tmp = head;
        while (tmp != null) {
            Node copy = tmp.next;
            copyTmp.next = copy;
            copyTmp = copyTmp.next;
            
            tmp.next = copy.next;
            tmp = tmp.next;
        }
        
        return copyDummy.next;
    }
}
```

