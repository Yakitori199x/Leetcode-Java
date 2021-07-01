# 394. Decode String (2021.02.15)

https://leetcode.com/problems/decode-string/

## Solution 1: Stack, iterative

- $TC:O(max(k) * maxLen(str))$
- $SC:O(max(k) * maxLen(str))$
- 虽然decode只需要遍历s，StringBuilder相关的操作也是O(1)，但是最后返回时toString的开销与解码后的字符串长度有关，upperbound就是最大的k乘以最长的子字符串。
- 该题的基本思想就是：遍历s，遇到数字时将其转化为int，遇到一般的字符时将其存入sb。
- 如果遇到"["，则将当前已经获取到的次数和子字符串压栈，且还原相应变量供后续生成。
- 如果遇到"]"，则将次数和子字符串弹出栈，此时的次数就代表cur需要重复几次，而弹出的prev则是之前已经解码的部分字符串，需要与cur进行拼接。

```java
class Solution {
    public String decodeString(String s) {
        Deque<Integer> digits = new LinkedList<>();
        Deque<StringBuilder> strs = new LinkedList<>();
        int k = 0;
        StringBuilder cur = new StringBuilder();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                k = k * 10 + c - '0';
            } else if (c == '[') {
                strs.push(cur);
                digits.push(k);
                // 重置cur和k，因为可能存在嵌套结构，比如3[a2[c]]
                cur = new StringBuilder();
                k = 0;
            } else if (c == ']') {
                // 将cur拼接到prev
                // prev可能是外层的子字符串，比如ab3[c]
                // 也可能是之前已经解码好的一部分字符串
                // 初始值为空
                StringBuilder prev = strs.pop();
                int times = digits.pop();
                for (int j = 0; j < times; j++) prev.append(cur);
                cur = prev;
            } else {
                cur.append(c);
            }
        }
        
        return cur.toString();
    }
}
```

## Solution 2: Recursion

- $TC:O(max(k) * maxLen(str))$
- $SC:O(max(k) * maxLen(str))$
- 时间复杂度总体上应该比Solution1要高，因为每层递归结束返回到caller时都要将sb先转换成String。
- 如果"["很多的话，时间复杂度就会很大。
- 递归的结束条件就是[1.遍历完整个字符串s]或是[2.遇到右括号，跳出循环]。
- 对于嵌套的情况，比如3[a2[c]]，右括号都是由下一层递归去处理，而不是caller去处理的。
- 在上面的例子中，遍历完2后遇到左括号，对剩下的chars进行递归。
- 在递归中遇到c和右括号，将存了c的sb转换成String返回到caller。

```java
class Solution {
    public String decodeString(String s) {
        Queue<Character> chars = new LinkedList<>();
        // 因为只想要对剩下的部分进行递归，把String转换为char的Queue更容易实现
        for (int i = 0; i < s.length(); i++) chars.offer(s.charAt(i));
        return decodeString(chars);
    }
    
    private String decodeString(Queue<Character> chars) {
        int k = 0;
        StringBuilder sb = new StringBuilder();
        while (!chars.isEmpty()) {
            char ch = chars.poll();
            if (Character.isDigit(ch)) {
                k = 10 * k + ch - '0';
            } else if (ch == '[') {
                String sub = decodeString(chars);
                for (int i = 0; i < k; i++) sb.append(sub);
                k = 0;
            } else if (ch == ']') {
                break;
            } else {
                sb.append(ch);
            }
        }
        
        return sb.toString();
    }
}
```