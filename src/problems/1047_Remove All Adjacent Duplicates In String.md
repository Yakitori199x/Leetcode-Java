# 1047. Remove All Adjacent Duplicates In String (2021.03.21)

https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/

## Similar Questions:
- 1209. Remove All Adjacent Duplicates in String II

## Solution 1: Two pointers

- $TC:O(n)$
- $SC:O(n)$
- 双指针模拟栈操作，i为栈指针，j为遍历S的字符的指针。

```java
class Solution {
    public String removeDuplicates(String S) {
        char[] chs = S.toCharArray();
        int i = 0, n = S.length();
        for (int j = 0; j < n; j++, i++) {
            chs[i] = chs[j];
            if (i > 0 && chs[i - 1] == chs[j]) {
                i -= 2;
            }
        }
        return new String(chs, 0, i);
    }
}
```

## Solution 2: Stack

- $TC:O(n)$
- $SC:O(n)$
- 遍历字符串s的每一个字符，如果栈为空或栈顶元素不等于当前字符，则将其入栈。
- 否则，若栈顶元素等于当前字符，说明有相邻的两个重复元素，则将栈顶元素出栈。
- 也可以直接用StringBuilder来充当stack。

```java
class Solution {
    public String removeDuplicates(String S) {
        Deque<Character> stack = new LinkedList<>();
        char[] chs = S.toCharArray();
        for (char ch : chs) {
            if (!stack.isEmpty() && stack.peek() == ch) {
                stack.pop();
            } else {
                stack.push(ch);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) sb.append(stack.pop());
        return sb.reverse().toString();
    }
}
```