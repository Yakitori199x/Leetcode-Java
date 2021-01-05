## 49. Group Anagrams (2020.05.13)

https://leetcode.com/problems/group-anagrams/

## Solution 1

- $TC:O(NKlogK)$
- $SC:O(NK)$, N is the length of strs, K is the length of longest string in strs
- Strings are anagrams if and noly if the sorted results are equal
- Sort each string in strs, and use the sorted result as key to group them

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) return new ArrayList<>();
        
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] elements = s.toCharArray();
            Arrays.sort(elements);
            String key = String.valueOf(elements);
            
            if (!map.containsKey(key)) map.put(key, new ArrayList<>());
            map.get(key).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
}
```

## Solution 2

- $TC:O(NK)$
- $SC:O(NK)$, N is the length of strs, K is the length of longest string in strs
- Strings are anagrams if and noly if the character counts are the same
- Count the occurrence of characters for each string, and use the counts result (concatenate to a string) as key to group them

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) return new ArrayList<>();
        
        Map<String, List<String>> map = new HashMap<>();
        
        for (String s : strs) {
            char[] charCounts = new char[26];
            for (char c : s.toCharArray()) charCounts[c - 'a']++;
            String key = String.valueOf(charCounts);
            if (!map.containsKey(key)) map.put(key, new ArrayList<>());
            map.get(key).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
}
```

