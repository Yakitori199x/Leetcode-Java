## 187. Repeated DNA Sequences (2020.08.16)

https://leetcode.com/problems/repeated-dna-sequences/

## Solution 1: Two hashsets

- $TC:O(NlogN)$, Because the length of target sequences is only 10. More specifically, the T(C) should be O(M*N), where M denotes the length of substring
- $SC:O(N)$
- One to record all the appearred substrings, the other to avoid adding substring repeated multiple times to result list more than once

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        if (s == null) return null;
        
        List<String> res = new ArrayList<>();
        Set<String> set = new HashSet<>();
        Set<String> setSub = new HashSet<>();
        
        for (int i = 0; i < s.length() - 9; i++) {
            String subStr = s.substring(i, i + 10);
            if (!set.add(subStr) && setSub.add(subStr)) res.add(subStr);
        }
        
        return res;
    }
}
```

## Solution 2: Bit manipulation

- $TC:O(N)$
- $SC:O(N)$
- 用00（0），01（1），10（2），11（3）来表示A，C，G，T四个字母，然后对于长度为10的substring，我们就去构造对应的二进制序列来表示它们（每次左移两位并和字母对应的整数进行按位与）

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        if (s == null) return null;
        
        List<String> res = new ArrayList<>();
        Set<Integer> set = new HashSet<>();
        Set<Integer> setSub = new HashSet<>();
        char[] map = new char[26];
        
        map['A' - 'A'] = 0;
        map['C' - 'A'] = 1;
        map['G' - 'A'] = 2;
        map['T' - 'A'] = 3;
        
        for (int i = 0; i < s.length() - 9; i++) {
            int bitSequence = 0;
            for (int j = i; j < i + 10; j++) {
                bitSequence <<= 2;
                bitSequence |= map[s.charAt(j) - 'A'];
            }
            
            if (!set.add(bitSequence) && setSub.add(bitSequence)) {
                res.add(s.substring(i, i + 10));
            }
        }
        
        return res;
    }
}
```

