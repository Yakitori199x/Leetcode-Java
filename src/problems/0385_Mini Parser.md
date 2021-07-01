# 385. Mini Parser (2021.03.23)

https://leetcode.com/problems/mini-parser/

```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *     // Constructor initializes an empty nested list.
 *     public NestedInteger();
 *
 *     // Constructor initializes a single integer.
 *     public NestedInteger(int value);
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // Set this NestedInteger to hold a single integer.
 *     public void setInteger(int value);
 *
 *     // Set this NestedInteger to hold a nested list and adds a nested integer to it.
 *     public void add(NestedInteger ni);
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return empty list if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
```

## Solution 1: Iterative

- $TC:O(n)$
- $SC:O(n)$
- 本题需要注意的是：new NestedInteger(i) -> i，是一个Integer。
- 而new NestedInteger().add(i) -> [i]，是只包含一个Integer的List。
- 我们要做的处理就是，如果遇到了左括号'['，说明在当前层级存在一个List，需要进入下一个层级，创建一个新的List。
- 如果遇到了右括号']'或者是','，首先我们看看start是否比当前的i来得小（start到i的范围就是要加入的数字）。
- 因为不论是右括号还是逗号，其左侧均有可能出现数字。（当然对于右括号，还可能有'..., [] ,...'）
- 接下来看看如果是右括号，说明当前层级遍历结束，我们还需要将当前层级的结果（保存在栈顶）加入到上一层的结果中。

```java
class Solution {
    public NestedInteger deserialize(String s) {
        if (s.charAt(0) != '[') return new NestedInteger(Integer.valueOf(s));
        
        Deque<NestedInteger> stack = new LinkedList<>();
        NestedInteger res = new NestedInteger();
        int start = 0;
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '[') {
                // 创建一个新的NestedInteger并压栈，之后将数或List加入其中
                NestedInteger ni = new NestedInteger();
                stack.push(ni);
                start = i + 1;
            } else if (ch == ']' || ch == ',') {
                // 存在待加入的数
                if (start < i) {
                    int val = Integer.valueOf(s.substring(start, i));
                    stack.peek().add(new NestedInteger(val));
                }
                
                // 当前层级遍历结束，将栈顶的NestedInteger弹出加入到上一层级的结果中
                if (ch == ']') {
                    res = stack.pop();
                    if (!stack.isEmpty()) stack.peek().add(res);
                }
                
                start = i + 1;
            }
        }
        return res;
    }
}
```

## Solution 2: Recursive

- $TC:O(n)$
- $SC:O(n)$
- 本题很明显可以根据括号来进行递归（一个左括号对应一个层次），其实就是利用递归栈来替代Solution1里面的stack。
- 我们维护i和start两个全局变量，遇到左括号则进行递归，并且将递归返回的结果加入当前的res。
- 遇到右括号则跳出循环，离开当前递归。其他的就是获取数值并加入res。
- 由于i和start是全局变量，在递归结束后仍然会保持在我们期望的位置（即进入递归时的左括号所匹配的右括号的下一个位置）。

```java
class Solution {
    // 跳过第一个左括号，因为后续的递归也会从左括号的下一个位置开始
    private int i = 1;
    private int start = 1;
    
    public NestedInteger deserialize(String s) {
        if (s.charAt(0) != '[') return new NestedInteger(Integer.valueOf(s));
        
        NestedInteger res = new NestedInteger();
        while (i < s.length()) {
            char ch = s.charAt(i);
            if (ch == '[') {
                // 先对i和start递增，否则递归会一直卡在这个左括号处
                start = ++i;
                NestedInteger next = deserialize(s);
                res.add(next);
            } else if (ch == ']' || ch == ',') {
                if (start < i) {
                    int val = Integer.valueOf(s.substring(start, i));
                    res.add(new NestedInteger(val));
                }
                
                start = ++i;
                if (ch == ']') {
                    break;
                }
            } else {
                i++;
            }
        }
        return res;
    }
}
```