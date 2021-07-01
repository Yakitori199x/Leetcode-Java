# 438. Find All Anagrams in a String (2021.04.28)

https://leetcode.com/problems/find-all-anagrams-in-a-string/

## Similar Questions:
- 567. Permutation in String

## Solution 1: Sliding Window

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        int i = 0, n = s.length(), m = p.length();
        // 因为这里的p只包含26个小写英文字符
        int[] hash = new int[26];
        // 记录p中每个字符的出现次数
        for (char c : p.toCharArray()) {
            hash[c - 'a']++;
        }
        
        // count表示对于当前窗口对应的子串，p中还剩余的待匹配的字符的个数，初始状态即为所有字符均未匹配
        int count = m;
        List<Integer> res = new ArrayList<>();
        for (int j = 0; j < n; j++) {
            // 向右移动窗口的右边界，扩大窗口的范围
            // 如果j对应的字符出现次数大于0，说明该字符是p中的字符且还有剩余未被匹配的部分（如果全被匹配了，对应的hash就会是小于等于0）
            // 这种情况就是找到了新的可匹配字符，对count扣除1
            if (hash[s.charAt(j) - 'a'] > 0) count--;
            // 将j对应字符的出现次数扣除1
            hash[s.charAt(j) - 'a']--;
            
            // 如果count为0，说明在当前窗口下，其对应的子串完全匹配了p中的所有字符，是一个anagram
            // 且该子串的起点即为窗口的左边界
            if (count == 0) res.add(i);
            
            // 如果当前窗口对应的子串长度已经达到了p.length()，我们就需要右移窗口的左边界，缩小窗口对应的范围
            if (j - i + 1 == m) {
                // 在对i处字符的hash累加之前，先判断该字符是否已经匹配了p中的字符，如果是则解除匹配，对count加1
                // 只有在累加之前，hash的值大于或等于的，才能证明i处字符对应p中的字符
                // 且没有被过度匹配（比如p中有一个a，而窗口内同时存在三个a，那对应的hash就会小于0。）
                if (hash[s.charAt(i) - 'a'] >= 0) count++;
                hash[s.charAt(i) - 'a']++;
                i++;
            }
        }
        return res;
    }
}
```