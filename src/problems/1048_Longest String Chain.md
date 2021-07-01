# 1048. Longest String Chain (2021.04.18)

https://leetcode.com/problems/longest-string-chain/

## Solution 1: DP

- $TC:O(n*s*s + nlogn)$
- $SC:O(n*s)$
- n为words的个数，s为word的长度，且根据本题条件，s最长不会超过16。
- 对于时间复杂度，其中O(nlogn)用于对words按长度进行排序，O(n*s*s)用于进行dp找到最长chain。
- 本题不是用一个数组来存储dp的状态，而是借助一个map来进行存储，因为此处dp状态的索引是String。
- dp[word]表示以该word作为最后一个单词的chain的最大长度，所以要求得dp[word]，就需要遍历word并每次删去一个字符，获得可能的前一个单词prev。
- dp[word]=max(dp[word], dp[prev]+1)（如果dp[prev]存在的话，但dp[word]至少等于1，对应只有word一个单词的chain）。
- 而我们要求的最大长度就是所有dp[word]中的最大者。

```java
class Solution {
    public int longestStrChain(String[] words) {
        Map<String, Integer> dp = new HashMap<>();
        // 先对words按长度排序，从较短的word开始递推
        Arrays.sort(words, (a, b) -> a.length() - b.length());
        int res = 0;
        for (String word : words) {
            int longest = 0;
            for (int i = 0; i < word.length(); i++) {
                // substring需要消耗O(s)，每次去除第i个字符
                String prev = word.substring(0, i) + word.substring(i + 1);
                longest = Math.max(longest, dp.getOrDefault(prev, 0) + 1);
            }
            dp.put(word, longest);
            res = Math.max(res, longest);
        }
        return res;
    }
}
```

## Solution 2: DFS + Memorization

- $TC:O(n)$
- $SC:O(1)$

```java

```