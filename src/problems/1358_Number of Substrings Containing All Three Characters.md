# 1358. Number of Substrings Containing All Three Characters (2021.04.29)

https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/

## Solution 1: Sliding Window

- $TC:O(n)$
- $SC:O(1)$
- 因为s中只存在a，b，c三个字符，我们可以用一个长度为3的数组来保存对这三个字符的计数。
- 同时，从i=0，j=0对应的window开始，向右移动j并记录字符出现的次数。
- 某个时刻a，b，c出现的次数均大于0时，说明当前window对应的子字符串是满足条件的（即同时包含了a，b，c）。
- 这时候我们就需要将i进行右移，同时扣除因i的右移而被移除window范围的字符的计数，直到对应window不满足条件为止。
- 这样一来，我们就可以确定有i个对应的子字符串是满足条件的，加入到最终的res中。
- 举个例子，比如我们有s = ababcabc，当j移动到第一个c，即j = 4时会停下，需要开始对i右移。
- 而i移动到i = 3，即第二个b处时，才能保证破坏window的合理性（让window对应的范围不能同时包含三个字符）。
- 此时，我们可以发现，加入i之前所有任意位置的字符，都可以和当前window构成一个合理的子串，比如abc，babc，ababc。
- 这些子串的数量即为i。

```java
class Solution {
    public int numberOfSubstrings(String s) {
        int[] count = new int[]{0, 0, 0};
        int i = 0, n = s.length(), res = 0;
        for (int j = 0; j < n; j++) {
            count[s.charAt(j) - 'a']++;
            while (count[0] > 0 && count[1] > 0 && count[2] > 0) {
                count[s.charAt(i) - 'a']--;
                i += 1;
            }
            res += i;
        }
        return res;
    }
}
```

## Solution 2: No Sliding Window

- $TC:O(n)$
- $SC:O(1)$
- 该解法是记录下在位置i处时，a，b，c三个字符对应的最后出现的位置。
- 对于每一个i，min(lastSeen)到i这一段子串必然同时包含三个字符（除了刚开始还未找到第一个满足条件的子串时）。
- 因此，[0, min(lastSeen)]的每个位置到i均是一个满足条件的解，其数量为min(lastSeen)+1。

```java
class Solution {
    public int numberOfSubstrings(String s) {
        // 初始值应当设为-1，否则还未第一次找到同时包含三个字符的子串时也会不断对res累加
        int[] lastSeen = new int[]{-1, -1, -1};
        int n = s.length(), res = 0;
        for (int i = 0; i < n; i++) {
            lastSeen[s.charAt(i) - 'a'] = i;
            res += Math.min(lastSeen[0], Math.min(lastSeen[1], lastSeen[2])) + 1;
        }
        return res;
    }
}
```