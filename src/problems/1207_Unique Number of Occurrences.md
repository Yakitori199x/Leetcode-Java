# 1207. Unique Number of Occurrences (2021.05.09)

https://leetcode.com/problems/unique-number-of-occurrences/

## Solution 1: Map + Set

- $TC:O(n)$
- $SC:O(n)$，arr的长度足够大且arr[i]的范围仍然是-1000到1000的话，可以视作O(1)
- 先统计每个num出现的次数，然后检查不同出现次数的个数和不同num的个数是否相等。

```java
class Solution {
    public boolean uniqueOccurrences(int[] arr) {
        Map<Integer, Integer> counts = new HashMap<>();
        for (int num : arr) counts.put(num, counts.getOrDefault(num, 0) + 1);
        return counts.size() == new HashSet<>(counts.values()).size();
    }
}
```