# 131. Palindrome Partitioning (2021.02.18)

https://leetcode.com/problems/palindrome-partitioning/

## Solution 1: DFS, Backtracking

- $TC:O(N*2^N)$
- $SC:O(N*2^N)$，如果不考虑返回的res的空间，那么只需要$O(N)$
- 本题的worst case（比如字符串中所有字符均相同的情况）需要遍历到所有可能的partition。
- 而一个长为N的字符串，有N-1个可以切分的位置，即存在2^(N-1)中情况。
- 此外，每次递归都需要消耗O(N)在substring以及isPalindrome上，最后将ans加入res时也需要耗费O(N)。
- 本题的关键在于每找到一个合适的substring，就对字符串剩下部分递归继续搜索（用strat来标记剩余部分），当搜索到字符串末尾时回溯。

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        List<String> ans = new ArrayList<>();
        partition(s, 0, ans, res);
        return res;
    }
    
    private void partition(String s, int start, List<String> ans, List<List<String>> res) {
        if (start == s.length()) {
            res.add(new ArrayList<>(ans));
        }
        
        // 固定住start，只需要移动end来确定partition的范围
        for (int end = start; end < s.length(); end++) {
            if (isPalindrome(s, start, end)) {
                ans.add(s.substring(start, end + 1));
                // 可以确定当前partition是回文的
                // 那么下一个partition的起始点就是end+1
                partition(s, end + 1, ans, res);
                ans.remove(ans.size() - 1);
            }
        }
    }
    
    private boolean isPalindrome(String s, int i, int j) {
        while (i <= j) {
            if (s.charAt(i++) != s.charAt(j--)) return false;
        }
        return true;
    }
}
```

## Solution 2: DFS + DP

- $TC:O(N*2^N)$
- $SC:O(N*2^N)$，如果不考虑返回的res的空间，那么只需要$O(N)$
- 思路与解法一相同，不同的是使用了dp来优化palindrome的判断，使这部分的时间复杂度降低到O(1)。
- 但是由于substring和ans深拷贝的开销仍然存在，所以不会有显著差异。

```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        List<String> ans = new ArrayList<>();
        // dp数组的空间开销可以进一步优化到O(N)，不过由于其他部分已占用的开销，优化意义不大
        boolean[][] dp = new boolean[s.length()][s.length()];
        partition(s, 0, dp, ans, res);
        return res;
    }
    
    private void partition(String s, int start, boolean[][] dp, List<String> ans, List<List<String>> res) {
        if (start == s.length()) {
            res.add(new ArrayList<>(ans));
        }
        
        for (int end = start; end < s.length(); end++) {
            if (s.charAt(start) == s.charAt(end) && (end - start <= 1 || dp[start + 1][end - 1])) {
                dp[start][end] = true;
                ans.add(s.substring(start, end + 1));
                partition(s, end + 1, dp, ans, res);
                ans.remove(ans.size() - 1);
            }
        }
    }
}
```