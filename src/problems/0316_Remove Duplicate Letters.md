# 316. Remove Duplicate Letters (2021.03.21)

https://leetcode.com/problems/remove-duplicate-letters/

## Solution 1: Greedy and Stack

- $TC:O(n)$
- $SC:O(n)$
- 本题不是单纯地去重，而是在保证获得的去重结果为最小字典序的字符串的情况下进行去重。
- 这就很容易想到贪心的思想，即在a和b都有重复的情况下，如果先发现b再发现a，那这个位于a之前的b就没有保留下来的意义。
- 因为以a开头（比b字典序小的字符）最终获得的字符串必然是比以b开头的字符串要来得小的。

```java
class Solution {
    public String removeDuplicateLetters(String s) {
        int n = s.length();
        Deque<Character> stack = new LinkedList<>();
        // 由于只有26个英文小写字母，我们可以使用定长数组来存储
        // cnt为s中每个字符的出现次数，用于判断是否重复以及还剩余多少相同字符
        // inStack用于过滤后续遍历到的已经存在stack中的字符
        int[] cnt = new int[26];
        boolean[] inStack = new boolean[26];
        char[] chs = s.toCharArray();
        
        // 统计每个字符的出现次数
        for (char ch : chs) cnt[ch - 'a']++;
        
        for (char ch : chs) {
            int idx = ch - 'a';
            // 每遍历一个字符，不管最终结果是直接跳过还是加入stack
            // 都需要把该字符的剩余数量减1
            cnt[idx]--;
            // 当前字符已经存在于stack中，没有继续判断的必要
            if (inStack[idx]) continue;
            
            // 当stack非空，栈顶字符大于当前字符且栈顶字符还有剩余的情况下（没有剩可能是因为本来就没有重复，或者是前面的被去重了只剩下这一个了）
            // 将栈顶字符出栈直到该条件不被满足
            while (!stack.isEmpty() && stack.peek() > ch && cnt[stack.peek() - 'a'] > 0) {
                inStack[stack.pop() - 'a'] = false;
            }
            
            // 将当前字符入栈且标记其inStack状态
            stack.push(ch);
            inStack[idx] = true;
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) sb.append(stack.pop());
        return sb.reverse().toString();
    }
}
```