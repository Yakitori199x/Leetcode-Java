# 1249. Minimum Remove to Make Valid Parentheses (2021.03.17)

https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/

## Similar Questions:
- 921. Minimum Add to Make Parentheses Valid

## Solution 1: Stack + Set

- $TC:O(n)$
- $SC:O(n)$
- 使用一个stack来保存当前括号的匹配，以及用一个set来保存当前应该被删除的括号位置。
- 算法：遍历s的每一个字符，如果遇到开括号，则将当前开括号的index入栈。
- 如果遇到闭括号，判断当前stack是否为空。stack非空则弹出栈顶元素，否则当前闭括号为多余的，需要被删除，加入到set。
- 遍历结束后，将stack中剩余的开括号的index一起加入到set。
- 最后再次遍历s，构建答案的时候略过set中对应位置的字符。

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        char[] chs = s.toCharArray();
        Deque<Integer> stack = new LinkedList<>();
        Set<Integer> pos = new HashSet<>();
        for (int i = 0; i < chs.length; i++) {
            if (chs[i] == '(') {
                stack.push(i);
            } else if (chs[i] == ')') {
                // 这里因为stack只保存了开括号的位置，stack没空就说明有剩余待匹配的开括号
                // 所以不需要判断栈顶元素是什么
                if (!stack.isEmpty()) stack.pop();
                else pos.add(i);
            }
        }
        
        while (!stack.isEmpty()) pos.add(stack.pop());
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < chs.length; i++) {
            if (!pos.contains(i)) sb.append(chs[i]);
        }
        return sb.toString();
    }
}
```

## Solution 2: Replacement

- $TC:O(n)$
- $SC:O(n)$
- 和Solution1的思路一致，只不过该解法直接对需要删除的位置的字符进行修改，统一设置为*，最后构造的时候一起删除。
- 由于Java不允许直接赋予空字符''，所以使用特定字符代替。
- 当然，这是基于本题条件是只存在小写英文字母的情况，如果涵盖了更多甚至所有的字符，那这个解法就不奏效了。

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        char[] chs = s.toCharArray();
        
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < chs.length; i++) {
            if (chs[i] == '(') {
                stack.push(i);
            } else if (chs[i] == ')') {
                if (!stack.isEmpty()) stack.pop();
                else chs[i] = '*';
            }
        }
        
        while (!stack.isEmpty()) chs[stack.pop()] = '*';
        StringBuilder sb = new StringBuilder();
        for (char ch : chs) {
            if (ch != '*') sb.append(ch);
        }
        return sb.toString();
    }
}
```