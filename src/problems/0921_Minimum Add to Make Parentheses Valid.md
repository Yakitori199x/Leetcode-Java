# 921. Minimum Add to Make Parentheses Valid (2021.03.17)

https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/

## Solution 1: Counting

- $TC:O(n)$
- $SC:O(1)$，这里将S转化成数组方便处理，如果不想要引入这个额外的空间开销，可以直接S.charAt(i)。
- 一般在处理括号匹配相关的问题时，我们会应用stack。比如判断S的括号是否匹配，我们只要维护一个stack然后进行入栈出栈操作即可。
- 但还存在一种方法，就是对括号计数。当然，完全遍历一遍判断计数的结果是不行的，因为有两种括号数量相等但不匹配的情况存在。
- 因此，我们要遍历的同时，对当前的计数进行监控。
- 比如我们用count来表示当前开括号的数量，如果遍历过程中count变成负数，说明这时闭括号多于开括号，无法匹配成功。
- 或者遍历完成后，count的值仍然大于0，说明还剩下没能被匹配的开括号。
- 本题的思想与上述类似，我们分别用open和close来表示当前等待被匹配的开括号数量和当前确定的未能被匹配（或者说需要添加括号进行匹配）的闭括号数量。
- 遍历的过程中，如果遇到开括号，就对open加1。如果遇到闭括号，若当前open大于0，说明还有开括号未匹配，那么对open减1。
- 否则，说明当前不存在未被匹配的开括号，则该闭括号是多余出来的，需要我们添加一个开括号才能匹配它，对close加1。
- 最终我们需要添加的括号数就是open与close之和。（最后可能会有多余的开括号）

```java
class Solution {
    public int minAddToMakeValid(String S) {
        int open = 0, close = 0;
        char[] chs = S.toCharArray();
        for (char ch : chs) {
            if (ch == '(') {
                open++;
            } else if (open > 0) {
                open--;
            } else {
                close++;
            }
        }
        return open + close;
    }
}
```

## Solution 2: Stack

- $TC:O(n)$
- $SC:O(n)$
- 遍历S的每个字符，遇到开括号入栈。
- 遇到闭括号则先判断栈是否为空且栈顶是否为开括号，如果是则匹配，把开括号弹出。
- 否则，将闭括号入栈。最终的答案就是留存在stack中的所有括号。

```java
class Solution {
    public int minAddToMakeValid(String S) {
        char[] chs = S.toCharArray();
        Deque<Character> stack = new LinkedList<>();
        for (char ch : chs) {
            if (ch == '(') {
                stack.push(ch);
            } else {
                if (!stack.isEmpty() && stack.peek() == '(') stack.pop();
                else stack.push(ch);
            }
        }
        return stack.size();
    }
}
```