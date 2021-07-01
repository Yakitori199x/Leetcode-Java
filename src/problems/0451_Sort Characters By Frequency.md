# 451. Sort Characters By Frequency (2021.04.27)

https://leetcode.com/problems/sort-characters-by-frequency/

## Solution 1: Sorting

- $TC:O(n)$
- $SC:O(n)$
- n为s的长度，因为已知s中只包含大小写英文字母和数字，所以map中的entry不会超过62个，其开销可以视作O(1)。
- 思路就是统计s中每个字符的出现频率并按照出现频率从大到小排序，最后构造出res。
- follow-up: 如果我们的要求是频率高且在s中出现位置靠前的字符排在前面，要如何构造呢？ -> 可以在保存到map的时候记录下字符对应的index，之后作为排序的指标之一

```java
class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> freq = new HashMap<>();
        for (char c : s.toCharArray()) {
            freq.put(c, freq.getOrDefault(c, 0) + 1);
        }
        
        List<Map.Entry<Character, Integer>> entries = new ArrayList<>(freq.entrySet());
        Collections.sort(entries, (a, b) -> b.getValue() - a.getValue());
        
        StringBuilder sb = new StringBuilder();
        for (Map.Entry<Character, Integer> entry : entries) {
            for (int i = 0; i < entry.getValue(); i++) sb.append(entry.getKey());
        }
        return sb.toString();
    }
}
```

## Solution 2: Bucket Sort

- $TC:O(n)$
- $SC:O(n)$
- 字符出现的频率最大就等于s的长度，因此可以使用桶排序。

```java
class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> freq = new HashMap<>();
        for (char c : s.toCharArray()) {
            freq.put(c, freq.getOrDefault(c, 0) + 1);
        }
        
        List<Character>[] bucket = new List[s.length() + 1];
        for (char c : freq.keySet()) {
            int f = freq.get(c);
            if (bucket[f] == null) bucket[f] = new ArrayList<>();
            bucket[f].add(c);
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = s.length(); i >= 0; i--) {
            if (bucket[i] != null) {
                for (char c : bucket[i]) {
                    for (int j = 0; j < i; j++) sb.append(c);
                }
            }
        }
        return sb.toString();
    }
}
```