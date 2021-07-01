# 524. Longest Word in Dictionary through Deleting (2021.04.23)

https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/

## Solution 1: Sorting

- $TC:O(m*(n*k*log(n*k)))$，m表示s的长度，n表示dictionary中的word的平均长度，k表示dictionary中word的个数。
- $SC:O(1)$
- 本题的核心应该是在于如何判断一个字符串是不是另一个字符串的子序列。
- 只要能够写出上述判断，那么本题只需要先对dictionary中的单词按照长度从大到小，长度相等则按字典序从小到大的规则进行排序。
- 然后找到dictionary中第一个s的子序列即可。

```java
class Solution {
    public String findLongestWord(String s, List<String> dictionary) {
        Collections.sort(
            dictionary, (a, b) -> a.length() == b.length() ? a.compareTo(b) : b.length() - a.length()
        );
        
        for (String word : dictionary) {
            if (isSubSequence(s, word)) return word;
        }
        return "";
    }
    
    // 判断t是否是s的子序列
    private boolean isSubSequence(String s, String t) {
        int i = 0;
        for (char ch : s.toCharArray()) {
            if (i < t.length() && ch == t.charAt(i)) i++;
        }
        return i == t.length();
    }
}
```

## Solution 2: No-sorting

- $TC:O(m*k)$，m表示s的长度，k表示dictionary中word的个数。
- $SC:O(1)$
- 对dictionary进行排序还是需要消耗比较多的时间的，特别是dictionary中长度相等的单词数很多的时候。
- 因此，我们可以不进行排序，直接遍历每一个单词并判断其是否是s的子序列。
- 如果word是s的子序列，再判断当前的res和word哪一个优先级更高。

```java
class Solution {
    public String findLongestWord(String s, List<String> dictionary) {
        String res = "";
        for (String word : dictionary) {
            if (isSubSequence(s, word)) {
                if (word.length() == res.length() && word.compareTo(res) < 0) res = word;
                if (word.length() > res.length()) res = word;
            }
        }
        return res;
    }
    
    private boolean isSubSequence(String s, String t) {
        int i = 0;
        for (char ch : s.toCharArray()) {
            if (i < t.length() && ch == t.charAt(i)) i++;
        }
        return i == t.length();
    }
}
```