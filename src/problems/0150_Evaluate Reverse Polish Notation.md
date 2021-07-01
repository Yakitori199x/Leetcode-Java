# 150. Evaluate Reverse Polish Notation (2021.02.27)

https://leetcode.com/problems/evaluate-reverse-polish-notation/

## Solution 1: Stack

- $TC:O(N)$
- $SC:O(N)$
- 后缀表达式的计算。
- 后缀表达式的优点就在于其计算不需要考虑运算符优先级和括号的问题，遍历表达式的每一个元素。
- 如果是数值则压栈，如果是运算符则弹出栈顶的两个元素并进行计算，然后把计算结果压栈即可。

```java
class Solution {
    public int evalRPN(String[] tokens) {
        if (tokens == null || tokens.length == 0) return 0;
        
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < tokens.length; i++) {
            String token = tokens[i];
            // 加法和乘法满足交换律，可以直接pop出来计算
            // 减法和除法则要注意顺序，是栈顶的第二个元素减去/除以栈顶元素
            if (token.equals("+")) {
                stack.push(stack.pop() + stack.pop());
            } else if (token.equals("-")) {
                int x = stack.pop(), y = stack.pop();
                stack.push(y - x);
            } else if (token.equals("*")) {
                stack.push(stack.pop() * stack.pop());
            } else if (token.equals("/")) {
                int x = stack.pop(), y = stack.pop();
                stack.push(y / x);
            } else {
                stack.push(Integer.valueOf(token));
            }
        }
        
        return stack.pop();
    }
}
```