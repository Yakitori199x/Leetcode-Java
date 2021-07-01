# 792. Number of Matching Subsequences (2021.04.09)

https://leetcode.com/problems/number-of-matching-subsequences/

## Solution 1: Parallel Matching

- $TC:O(m + sum(len(words)))$
- $SC:O(n)$，m为s的长度，n为words的数量，然后我们用k来表示最长word的长度
- Naive的思路就是遍历每一个word，然后对于每一个word，遍历s来查看word是不是一个子序列。
- 这种解法的时间复杂度为O(m*n)，当s很长且待匹配的字符位于s的末尾（或者压根不存在能够完全匹配的字符）的情况下开销很大。
- 因此，Solution1采用了一种并行检查的思想，维护一个List来记录当前待匹配字符为a-z的对应words（初始状态为words的首字符）。
- 然后对s进行遍历，每遇到一个字符c，就将List中c对应的words的待匹配字符更新到下一个，并且保存到List中对应的新位置。
- 当然，如果某个word已经被完全匹配，我们就将其保存到List[0]或是直接累加res。

```java
class Solution {
    public int numMatchingSubseq(String s, String[] words) {
        // 这里也可以使用长度为26的List，搭配List[char - 'a']使用
        // 为了方便实现，我们就直接使用长度128的List，每个元素为pair的List
        List<Integer[]>[] wait = new List[128];
        for (int c = 0; c <= 'z'; c++) wait[c] = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            wait[words[i].charAt(0)].add(new Integer[]{i, 1});
        }
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            List<Integer[]> next = new ArrayList(wait[c]);
            wait[c] = new ArrayList<>();
            // 这一部分也可以用StringBuilder每次去删除首字符来实现
            // 但是对应的操作时间复杂度为O(k)，所以还是使用Pair来记录索引比较高效
            for (Integer[] pair : next) {
                if (pair[1] == words[pair[0]].length()) {
                    // 当前word的所有字符均被匹配，加入到最终答案的List中
                    wait[0].add(pair);
                } else {
                    // 更新到下一个待匹配字符
                    wait[words[pair[0]].charAt(pair[1]++)].add(pair);
                }
            }
        }
        
        return wait[0].size();
    }
}
```