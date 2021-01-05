## 331. Verify Preorder Serialization of a Binary Tree (2020.06.16)

https://leetcode.com/problems/verify-preorder-serialization-of-a-binary-tree/

## Solution 1

- $TC:O(N)$
- $SC:O(N)$ -> can reduce to O(1) by iterating the preorder string in place
- A non-null node has 1 indegree (+1) and 2 outdegree (-2)
- A null node has 1 indegree and 0 outdegree
- When we iterate the serialization, it should always have the sum of indegree and outdegree not exceed 0 and it equals to 0 after the iteration
- p.s. 如果给出一个binary tree的前序遍历序列，那么该序列一定满足以上特性（中序，后序或非法树均不满足）
- 那么我们想知道的就是，如果给出一个序列满足该特性，这个序列一定对应着某棵树的前序遍历吗？
- 假设该序列不是某棵树的前序遍历，那么其可能是中序或后序遍历，由之前的结论可知这一情况是不成立的
- 还有一种情况则是该序列为某非法树的某种遍历，这一情况还不知道怎么证明，但是如果某棵非法树的遍历序列也满足该特性，
- 应该同时也可以找到对应的合法树，该树的前序遍历等于给出的遍历序列（此处不太严谨）

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        String[] nodes = preorder.split(",");
        int degree = -1;
        
        for (String node : nodes) {
            degree++;
            if (degree > 0) return false;
            
            if (!node.equals("#")) {
                degree -= 2;
            }
        }
        
        return degree == 0;
    }
}
```

## Solution 2: Stack

- $TC:O(N)$
- $SC:O(N)$
- 如果遇到非空节点，直接入栈
- 如果遇到空节点，但是栈为空（可能是遍历到最后一个节点一层一层往上pop的情况）或是栈顶不为空节点，也直接入栈
- 除以上情况外，先把栈顶的空节点pop出来，检查一下栈是否空了，若非空，则再pop一个节点出栈，不断循环直到栈顶为非空节点
- 其实相当于找到一对空节点，则将两个节点连同其父节点简化为一个空节点

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        String[] nodes = preorder.split(",");
        Stack<String> stack = new Stack<>();
        
        for (String node : nodes) {
            while (node.equals("#") && !stack.isEmpty() && stack.peek().equals("#")) {
                stack.pop();
                if (stack.isEmpty()) return false;
                stack.pop();
            }
            
            stack.push(node);
        }
        
        return (stack.size() == 1 && stack.peek().equals("#"));
    }
}
```

