# 1209. Remove All Adjacent Duplicates in String II (2021.03.16)

https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/

## Solution 1: 2-pointers

- $TC:O(n)$
- $SC:O(n)$
- 该解法就是把s转化为char数组作为stack，并维护两个指针i和j。
- i是栈顶指针，用于模拟栈的push和pop操作。j则负责遍历s中的每一个字符，j对应的就是当前待遍历的字符。
- 在对stack进行遍历和操作的过程中，我们同时维护了一个count用来表示stack中对应字符出现的次数。

```java
class Solution {
    public String removeDuplicates(String s, int k) {
        int i = 0, n = s.length();
        int[] count = new int[n];
        char[] stack = s.toCharArray();
        for (int j = 0; j < n; j++) {
            // 相当于把当前字符直接入栈
            stack[i] = stack[j];
            // 更新当前字符stack[j]对应的count，如果栈为空（i>0）或是stack[j]和之前的栈顶字符不相同
            // 那么就是一个全新的字符，count设置为1。否则，基于之前已经出现的次数累加。
            count[i] = (i > 0 && stack[i - 1] == stack[j]) ? count[i - 1] + 1 : 1;
            // 栈顶字符出现次数到达k次，模拟出栈过程，直接回退栈顶指针
            if (count[i] == k) i -= k;
            i++;
        }
        
        StringBuilder sb = new StringBuilder();
        for (int j = 0; j < i; j++) sb.append(stack[j]);
        return sb.toString();
    }
}
```

## Solution 2: 2-Stacks

- $TC:O(n)$
- $SC:O(n)$
- 原理和过程与Solution1相同，只不过分别使用了charStk和countStk两个栈。

```java
class Solution {
    public String removeDuplicates(String s, int k) {
        int n = s.length();
        Deque<Character> charStk = new LinkedList<>();
        Deque<Integer> countStk = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (!charStk.isEmpty() && charStk.peek() == c) {
                countStk.push(countStk.peek() + 1);
            } else {
                countStk.push(1);
            }
            charStk.push(c);
            
            if (countStk.peek() == k) {
                for (int j = 0; j < k; j++) {
                    charStk.pop();
                    countStk.pop();
                }
            }
        }
        
        StringBuilder sb = new StringBuilder();
        while (!charStk.isEmpty()) sb.append(charStk.pop());
        return sb.reverse().toString();
    }
}
```

## Solution 3: 1-Stack

- $TC:O(n)$
- $SC:O(n)$
- 通过定义Adjacent来把Solution2中的charStk和countStk合并为一个栈。

```java
class Solution {
    public String removeDuplicates(String s, int k) {
        int n = s.length();
        Deque<Adjacent> stack = new LinkedList<>();
        for (char c : s.toCharArray()) {
            if (!stack.isEmpty() && stack.peek().c == c) {
                stack.peek().count++;
            } else {
                stack.push(new Adjacent(c, 1));
            }
            
            if (stack.peek().count == k) stack.pop();
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            Adjacent adjacent = stack.pop();
            for (int i = 0; i < adjacent.count; i++) sb.append(adjacent.c);
        }
        return sb.reverse().toString();
    }
    
    class Adjacent {
        public char c;
        public int count;
        
        public Adjacent(char c, int count) {
            this.c = c;
            this.count = count;
        }
    }
}
```

## Solution 4: StringBuilder as Stack

- $TC:O(n)$
- $SC:O(n)$

```java
https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/discuss/392933/JavaC%2B%2BPython-Two-Pointers-and-Stack-Solution
```