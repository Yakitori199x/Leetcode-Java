## 242. Valid Anagram (2020.08.13)

https://leetcode.com/problems/valid-anagram/

## Solution 1: Anagram: 相同字母异序词

- $TC:O(N)$
- $SC:O(1)$, because the string contains only lowercase alphabets
- 因为string只包含了26个小写字母，所以可以创建一个数组来记录每个字母的次数
- 对s中出现的每个字母，将其次数+1；对t中出现的每个字母，将其次数-1。
- 如果最终每个字母的出现次数为0（相互抵消），那么证明是Anagram。

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s == null || t == null) return true;
        if (s.length() != t.length()) return false;
        
        int[] numOfChars = new int[26];
        for (int i = 0; i < s.length(); i++) {
            numOfChars[s.charAt(i) - 'a']++;
            numOfChars[t.charAt(i) - 'a']--;
        }
        
        for (int num : numOfChars) {
            if (num != 0) return false;
        }
        
        return true;
    }
}
```

## Follow-up: What if the inputs contain unicode characters? How would you adapt your solution to such case?
- $TC:O(N)$
- $SC:O(M)$, M is the number of unique characters in s&t
- 如果string包含了其他的Unicode字符，那么我们就需要用一个hashmap来进行计数

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s == null || t == null) return true;
        if (s.length() != t.length()) return false;
        
        Map<Character, Integer> map = new HashMap<>();
        
        for (int i = 0; i < s.length(); i++) {
            char charS = s.charAt(i);
            if (!map.containsKey(charS)) map.put(charS, 1);
            else map.put(charS, map.get(charS) + 1);
            
            char charT = t.charAt(i);
            if (!map.containsKey(charT)) map.put(charT, -1);
            else map.put(charT, map.get(charT) - 1);
        }
        
        for (char c : map.keySet()) {
            if (map.get(c) != 0) return false;
        }
        
        return true;
    }
}
```

