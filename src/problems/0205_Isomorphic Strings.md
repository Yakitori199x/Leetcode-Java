## 205. Isomorphic Strings (2020.08.13)

https://leetcode.com/problems/isomorphic-strings/

## Solution 1

- $TC:O(N)$
- $SC:O(N)$
- 不使用HashMap来构建char到char的mapping，而是将其map到对应的index。
- 具体来说，就是在遍历的时候，对于一组字符的映射(a, b)，记录下这两个字符当前对应的index。
- 在这种情况下，chars[a] = chars[b]代表了(a, b)具有对应关系或是a和b还没有出现在当前字符串里。
- 如果后续遇到的pair不满足chars[a] = chars[b]，则说明可能存在a映射到t中的两个不同字符，或是s中的两个不同字符映射到了b

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        int len = s.length();
        // 因为ASCII编码只包含了256个不同字符，所以chars[0:256)对应了s，chars[256:512)对应了t。
        int[] chars = new int[512];
        
        for (int i = 0; i < len; i++) {
            if (chars[s.charAt(i)] != chars[t.charAt(i) + 256]) return false;
            chars[s.charAt(i)] = chars[t.charAt(i) + 256] = i + 1;
        }
        
        return true;
    }
}
```

## Solution 2: HashMap + HashSet

- $TC:O(N)$
- $SC:O(N)$
- hashmap用来记录charS到charT的映射
- 分两种情况，若当前字符charS已经是map里的key，则需要检查其对应的value是否等于charT，不等于则说明charS对应了两个不同的charT
- 若charS是新遇到的字符，则需要检查当前的charT是否已经存在对应字符, 该情况下可以使用map.containsValue(charT)，但是时间复杂度为O(N)。
- 因此，我们可以使用一个hashset来记录已经出现过的charT（说明charT已经在s中有对应的charS）。
- 若set中不存在charT，说明该映射是合法的，将其加入map，并把charT加入set。

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        int len = s.length();
        Map<Character, Character> map = new HashMap<>();
        Set<Character> set = new HashSet<>();
        
        for (int i = 0; i < len; i++) {
            char charS = s.charAt(i), charT = t.charAt(i);
            if (map.containsKey(charS)) {
                if (!map.get(charS).equals(charT)) return false;
            } else {
                if (set.contains(charT)) return false;
                map.put(charS, charT);
                set.add(charT);
            }
        }
        
        return true;
    }
}
```

