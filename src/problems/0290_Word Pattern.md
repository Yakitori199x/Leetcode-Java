## 290. Word Pattern (2020.08.13)

https://leetcode.com/problems/word-pattern/

## Solution 1: Similar to the first solution of 205. Isomorphic Strings

- $TC:O(N)$
- $SC:O(N)$
- Index Hash Map

```java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        if (pattern == null || str == null) return false;
        
        String[] strs = str.split(" ");
        if (pattern.length() != strs.length) return false;
        
        // 也可以只用一个general hashmap -> Map map = new HashMap<>();
        // 但不好的是这样没法让compiler帮忙类型检查
        // 而且还存在像a这样既是char又可以是word的情况，因此还是推荐维护两个map
        Map<Character, Integer> mapChar = new HashMap<>();
        Map<String, Integer> mapStr = new HashMap<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            if (!Objects.equals(mapChar.put(pattern.charAt(i), i), mapStr.put(strs[i], i))) return false;
        }
        
        return true;
    }
}
```

## Solution 2: Similar to the second solution of 205. Isomorphic Strings

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        if (pattern == null || str == null) return false;
        
        String[] strs = str.split(" ");
        if (pattern.length() != strs.length) return false;
        
        Map<Character, String> map = new HashMap<>();
        Set<String> set = new HashSet<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            if (map.containsKey(c)) {
                if (!map.get(c).equals(strs[i])) return false;
            } else {
                if (set.contains(strs[i])) return false;
                map.put(c, strs[i]);
                set.add(strs[i]);
            }
        }
        
        return true;
    }
}
```

