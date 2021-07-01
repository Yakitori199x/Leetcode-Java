# 424. Longest Repeating Character Replacement (2021.05.08)

https://leetcode.com/problems/longest-repeating-character-replacement/

## Solution 1: Sliding Window

- $TC:O(n)$
- $SC:O(1)$
- 先扩展窗口直到窗口对应的子串中需要替换的字符数超过k（通过统计窗口内出现次数最多的字符决定，窗口大小减去出现最多的字符即为需要替换的字符数）。
- 因为我们想求的是最大长度，所以我们可以保持当前窗口长度，在扩展右边界的同时检查是否缩小左边界，直到遍历完整个字符串。

```java
class Solution {
    public int characterReplacement(String s, int k) {
        int[] counts = new int[26];
        int i = 0, j = 0, maxCount = 0, n = s.length();
        while (j < n) {
            counts[s.charAt(j) - 'A']++;
            maxCount = Math.max(maxCount, counts[s.charAt(j) - 'A']);
            if (j - i + 1 - maxCount > k) {
                counts[s.charAt(i) - 'A']--;
                i++;
            }
            j++;
        }
        return j - i;
    }
}
```