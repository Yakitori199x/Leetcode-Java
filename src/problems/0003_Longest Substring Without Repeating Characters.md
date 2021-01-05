## 3. Longest Substring Without Repeating Characters (2020.05.21)

https://leetcode.com/problems/longest-substring-without-repeating-characters/

## Solution 1: Brute force

- $TC:O(n^3)$
- $SC:O(m)$, `m is the size of character set / the kinds of characters in string` 
- Check all substrings and find the longest one without duplicate characters

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null) return 0;
        
        int res = 0;
        int len = s.length();
        for (int i = 0; i < len; i++) {
            for (int j = i + 1; j <= len; j++) {
                if (!hasRepeatChar(s, i, j)) 
                    res = Math.max(res, j - i);
            }
        }
        
        return res;
    }
    
    private boolean hasRepeatChar(String s, int i, int j) {
        Set<Character> set = new HashSet<>();
        
        for (int k = i; k < j; k++) {
            char ch = s.charAt(k);
            if (set.contains(ch)) return true;
            else set.add(ch);
        }
        
        return false;
    }
}
```

## Solution 2: Naive sliding window

- $TC:O(n^2)$
- $SC:O(m)$
- If the right pointer move to j and find $s_{i, j}$ has duplicate, then it is unnecessary to check successive substrings start from i.
- And we don't need to move back the right pointer for checking substrings between $s_{i + 1, j - 1}$ again.

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null) return 0;
        
        int res = 0;
        int len = s.length();
        int i = 0, j = i + 1;
        while (j <= len) {
            if (hasRepeatChar(s, i, j)) i++;
            else {
                res = Math.max(res, j - i);
                j++;
            }
        }
        
        return res;
    }
    
    private boolean hasRepeatChar(String s, int i, int j) {
        Set<Character> set = new HashSet<>();
        
        for (int k = i; k < j; k++) {
            char ch = s.charAt(k);
            if (set.contains(ch)) return true;
            else set.add(ch);
        }
        
        return false;
    }
}
```

## Solution3: Sliding window

- $TC:O(n)$, specifically, $O(2n)$ in the worst case (all characters are the same)
- $SC:O(m)$
- It is unnecessary to check if duplicate characters exist by iterating the substring
- If we checked that $s_{i, j-1}$ is legal, we only need to check whether $s_{j}$ is already in $s_{i, j-1}$, it can be implemented by HashSet

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null) return 0;
        
        int res = 0;
        int len = s.length();
        int i = 0, j = 0;
        
        Set<Character> set = new HashSet<>();
        while (j < len) {
            if (set.contains(s.charAt(j))) {
                set.remove(s.charAt(i++));
            } else {
                set.add(s.charAt(j++));
                res = Math.max(res, set.size());
            }
        }
        
        return res;
    }
}
```

## Solution4: Sliding window - optimized

- $TC:O(n)$, faster than Sliding window in many cases, but it is still O(2*n) when all characters are the same
- $SC:O(m)$
- Use hash table to maintain the mapping of character to its index, we can directly increase i to (index of duplicate character + 1) instead of increasing it step by step
- As i will skip duplicate character, it is fine to calculate length include character in j and add/update it to map

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null) return 0;
        
        int res = 0;
        int len = s.length();
        
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0, j = 0; j < len; j++) {
            if (map.containsKey(s.charAt(j))) {
            	// because i will be set as (j' + 1), some characters between [i', j' - 1] still be in map
            	// but these charaters are not belong to substring from i to j
                i = Math.max(map.get(s.charAt(j)), i);
            }
            
            res = Math.max(res, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        
        return res;
    }
}
```

