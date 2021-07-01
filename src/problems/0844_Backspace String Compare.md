# 844. Backspace String Compare (2021.04.08)

https://leetcode.com/problems/backspace-string-compare/

## Solution 1: Stack

- $TC:O(m+n)$
- $SC:O(m+n)$
- 这里用StringBuilder来模拟Stack。

```java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        return build(S).equals(build(T));
    }
    
    private String build(String s) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch != '#') sb.append(ch);
            else if (sb.length() > 0) sb.setLength(sb.length() - 1);
        }
        return sb.toString();
    }
}
```

## Solution 2: Reverse traversal

- $TC:O(m+n)$
- $SC:O(1)$
- O(1)空间复杂度的解法。由题设可知，字符串中的某个非#的字符最终能否留存下来，取决于其后的#。
- 因此，我们可以想到反向遍历字符串，当我们遇到一个非#的字符，且当前累积的#数量为0时，说明该字符可以保留下来作为最终结果里的一个字符。
- 我们同时对s和t进行从右到左的遍历，每次都寻找s和t中下一个可以作为最终结果的字符。
- 如果找到的两个字符不相等，说明s和t的最终结果也不可能相等，因为这两个字符同是s和t的倒数第k个字符。
- 又或者是当s或t已经被遍历完了，而另一个字符串还有剩余非#的字符时，也代表最终结果不相等。

```java
class Solution {
    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1, j = t.length() - 1;
        int cntS = 0, cntT = 0;
        while (i >= 0 || j >= 0) {
            // 找到s的下一次可作为最终结果的字符
            // 只要s还未被完全遍历，且当前累积的#数量大于0或当前字符为#，就可以进入该循环
            while (i >= 0 && (cntS > 0 || s.charAt(i) == '#')) {
                if (s.charAt(i--) == '#') cntS++;
                else cntS--;
            }
            
            // 找到t的下一次可作为最终结果的字符
            while (j >= 0 && (cntT > 0 || t.charAt(j) == '#')) {
                if (t.charAt(j--) == '#') cntT++;
                else cntT--;
            }
            
            // 这一轮找到的s和t中满足条件的字符不相等
            if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) return false;
            // s和t的其中一个被完全遍历，而另一个还未被完全遍历
            if ((i < 0) ^ (j < 0)) return false;
            i--; j--;
        }
        return true;
    }
}
```