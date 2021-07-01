# 856. Score of Parentheses (2021.03.15)

https://leetcode.com/problems/score-of-parentheses/

## Solution 1: Divide-and-Conquer

- $TC:O(n^2)$
- $SC:O(n)$
- 最坏的情况会在这样的测试用例取到：(((((((....)))))))，即每次都要遍历完外侧的两个括号才能得到count=0，然后才能递归下去。
- 分治解法的基本思想在于，我想要求得S这个balanced parenthesis的分数，我就可以把S分解成更小的balanced parenthesis。
- 即Score(S) = Score(P1) + Score(P2) + ... + Score(Pn)。
- 那么要如何确定一个balanced parenthesis呢？那就是在遍历过程中，左括号数量和右括号数量匹配的时刻(count == 0)。
- 这时候如果是一组()，那就是我们的base case，对res累加1即可。
- 否则说明这个balanced parenthesis是一个嵌套的结构，那么对这个范围进行递归计算即可，最后把返回的分数乘以2加到res上。

```java
class Solution {
    public int scoreOfParentheses(String S) {
        return scoreOfParentheses(S, 0, S.length());
    }
    
    private int scoreOfParentheses(String S, int i, int j) {
        int count = 0, res = 0;
        for (int k = i; k < j; k++) {
            if (S.charAt(k) == '(') count++;
            else count--;
            
            if (count == 0) {
                if (k - i == 1) res += 1;
                else res += 2 * scoreOfParentheses(S, i + 1, k);
                i = k + 1;
            }
        }
        return res;
    }
}
```

## Solution 2: Stack

- $TC:O(n)$
- $SC:O(n)$
- 我们可以把balanced parenthesis看做一个层级结构，在stack中维护每一层对应的score。
- 在遍历S的时候，遇到左括号就说明进入了新的一层，向stack中加入该层最初的分数0，并以该层作为当前层。
- 如果遇到右括号，说明对当前层的遍历结束，即将离开该层并返回到上层，那么最终上层的分数就应该是prev + Math.max(2 * cur, 1)。
- 其中，prev为上层之前已经计算出的分数，而当前层的分数至少是1，即()。
- 当然也可能是更大的嵌套结构，比如(...)，那么就需要将当前层的分数乘以2。
- 举一个更详细的例子以帮助理解（.的位置代表所处层级）：
- .(()(())) depth=0 stack=[0]
- (.()(())) depth=1 stack=[0,0]
- ((.)(())) depth=2 stack=[0,0,0]
- (().(())) depth=1 stack=[0,1]
- (()(.())) depth=2 stack=[0,1,0]
- (()((.))) depth=3 stack=[0,1,0,0]
- (()(().)) depth=2 stack=[0,1,1]
- (()(()).) depth=1 stack=[0,3]
- (()(())). depth=0 stack=[6]

```java
class Solution {
    public int scoreOfParentheses(String S) {
        Deque<Integer> stack = new LinkedList<>();
        stack.push(0);
        for (int i = 0; i < S.length(); i++) {
            if (S.charAt(i) == '(') stack.push(0);
            else {
                int cur = stack.pop();
                int prev = stack.pop();
                stack.push(prev + Math.max(2 * cur, 1));
            }
        }
        return stack.peek();
    }
}
```

## Solution 3: Count Cores

- $TC:O(n)$
- $SC:O(1)$
- 我们把()定义为core，这个结构是唯一能够直接提供value的，而外部嵌套着core的结构其实只是基于core添加了乘数。
- 在遍历的过程中，每遇到一个左括号，我们就知道进入了下一层，可以对level（或者叫depth）加1。
- 如果遇到右括号，则表示离开了当前层，需要对level减去1。同时，如果左侧为(，说明构成了一个core，我们可以直接将2的当前level次方乘以core的分数，添加到res上。
- 为什么可以这样呢？举个例子，比如：(()(())) = (()) + ((()))，抑或是(()()()) = (()) + (()) + (())。
- 也就是说，我们只需要关注core，以及当前core所对应的层级或深度即可。
- 还有一种视角是将balanced parenthesis看成树结构来解释。
- 参考：https://leetcode.com/problems/score-of-parentheses/discuss/141777/C%2B%2BJavaPython-O(1)-Space (th015的comment)

```java
class Solution {
    public int scoreOfParentheses(String S) {
        int res = 0, level = 0;
        for (int i = 0;  i < S.length(); i++) {
            if (S.charAt(i) == '(') level++;
            else {
                level--;
                if (S.charAt(i - 1) == '(') res += 1 << level;
            }
        }
        return res;
    }
}
```