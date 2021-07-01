# 1190. Reverse Substrings Between Each Pair of Parentheses (2021.03.12)

https://leetcode.com/problems/reverse-substrings-between-each-pair-of-parentheses/

## Solution 1: Naive iteration

- $TC:O(n^2)$
- $SC:O(n)$
- 遍历字符串的每一个字符，如果遇到英文字符，直接加入res构成当前状态下的结果。
- 如果遇到左括号，则将当前已构成的res的长度压入栈中，这个值就表示了之后遇到右括号时需要翻转的字符串的起始位置。
- 如果遇到右括号，弹出栈顶与其匹配的左括号所对应的res的位置，翻转以该位置为起点，直到当前res末尾的这部分字符。

```java
class Solution {
    public String reverseParentheses(String s) {
        if (s == null || s.length() == 0) return s;
        
        List<Character> res = new ArrayList<>();
        Deque<Integer> stack = new LinkedList<>();
        
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '(') {
                // 注意加入的是res的size，而不是左括号的index
                stack.push(res.size());
            } else if (ch == ')') {
                int j = stack.pop();
                reverse(res, j, res.size() - 1);
            } else {
                res.add(ch);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (char ch : res) sb.append(ch);
        return sb.toString();
    }
    
    private void reverse(List<Character> s, int begin, int end) {
        while (begin <= end) {
            char temp = s.get(begin);
            s.set(begin++, s.get(end));
            s.set(end--, temp);
        }
    }
}
```

## Solution 2: Wormholes

- $TC:O(n)$
- $SC:O(1)$
- 非常巧妙的一种解法，详见：https://leetcode.com/problems/reverse-substrings-between-each-pair-of-parentheses/discuss/383670/JavaC%2B%2BPython-Tenet-O(N)-Solution
- 基本思想就是首先把每一对匹配的括号的对应位置记录下来，比如某个左括号的位置为3，其对应的右括号的位置为6，那么pairs[3]=6，pairs[6]=3，这个过程借助到stack来记录左括号的位置以及匹配关系。
- 之后就是从头开始遍历字符串，遍历过程中维护一个direction来表示i的移动方向。
- 如果遇到括号，则将i直接设置到该括号所匹配的括号处，反向遍历，直到遇到下一个括号。
- 虽然i不断地跳跃且遍历方向也不断改变，但最终i会在最外层的右括号处向右遍历直到字符串的结尾。

```java
class Solution {
    public String reverseParentheses(String s) {
        if (s == null || s.length() == 0) return s;
        
        // 将括号及其匹配的括号的位置互相记录下来
        int[] pairs = new int[s.length()];
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '(') stack.push(i);
            if (ch == ')') {
                int pos = stack.pop();
                pairs[pos] = i;
                pairs[i] = pos;
            }
        }
        
        StringBuilder sb = new StringBuilder();
        int direction = 1;
        for (int i = 0; i < s.length(); i += direction) {
            char ch = s.charAt(i);
            // 遇到括号，直接跳跃至与其匹配的括号的位置，并调转遍历方向
            if (ch == '(' || ch == ')') {
                i = pairs[i];
                direction = -direction;
            } else {
                sb.append(ch);
            }
        }
        
        return sb.toString();
    }
}
```