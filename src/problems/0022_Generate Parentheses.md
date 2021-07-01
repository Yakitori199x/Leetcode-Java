# 22. Generate Parentheses (2021.02.22)

https://leetcode.com/problems/generate-parentheses/

## Solution 1: DFS, brute-force

- $TC:O(n*2^(2n))$
- $SC:O(n)$
- 最单纯最直接的backtracking。

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        dfs("", n, res);
        return res;
    }
    
    private void dfs(String current, int n, List<String> res) {
        // 根据已经构成的当前字符串的长度来判断是否用到了n对括号
        if (current.length() == 2 * n) {
            if (isValid(current)) res.add(current);
            return ;
        }
        
        // 只有两种可能的情况，所以不需要写循环
        dfs(current + "(", n, res);
        dfs(current + ")", n, res);
    }
    
    // 因为本题只有圆括号，可以从左到右遍历字符串
    // 如果遍历过程中出现右括号的数量大于左括号的情况（balance < 0），说明不是valid
    // 遍历结束后再检查一下左右括号的数量是否匹配（balance == 0）
    private boolean isValid(String s) {
        int balance = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') balance++;
            else balance--;
            
            if (balance < 0) return false;
        }
        
        return balance == 0;
    }
}
```

## Solution 2: DFS, optimized

- $TC:O(n*2^(2n))$，实际上的复杂度没这么高，应该是$O(4^n/sqrt(n))$，与Catalan数有关，但不容易推导出来。
- $SC:O(n)$
- 做DFS的同时记录左括号和右括号的个数，并以此来控制始终生成有效的结果，达到给定的长度后无需验证直接加入最终的答案

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        dfs("", n, 0, 0, res);
        return res;
    }
    
    private void dfs(String current, int n, int open, int close, List<String> res) {
        if (current.length() == 2 * n) {
            res.add(current);
            return ;
        }
        
        // 通过open和close来控制左括号和右括号的生成
        // 左括号的数量不能超过n，右括号的数量不能超过左括号
        // 这样生成出来的字符串必定是有效的
        if (open < n) {
            dfs(current + "(", n, open + 1, close, res);
        }
        
        if (close < open) {
            dfs(current + ")", n, open, close + 1, res);
        }
    }
}
```

## Solution 3: DP

- $TC:O(4^n/sqrt(n))$
- $SC:O(4^n/sqrt(n))$
- TC和SC与Catalan数有关，不容易推导出来，这里主要掌握dp的思路。
- 本题的dp[i]表示i对括号对应能够组成的有效字符串，比如dp[0]=""，dp[1]="()"。
- 通过dp[2]: "("dp[0]")"f(1), "("dp[1]")"和dp[3]: "("dp[0]")"dp[2], "("dp[1]")"dp[1], "("dp[2]")"可以看出
- 本题的状态转移方程为：dp[n] = "("dp[0]")"dp[n-1], "("dp[1]")"dp[n-2], ..., "("dp[i]")"dp[n-1-i], ..., "(dp[n-1]")"
- 即dp[n] = "("dp[i]")"dp[n-1-i] (0 <= i < n)
- 需要注意的是dp[i]是一个字符串的list，其中的每一个字符串都要参与组成新的有效字符串

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<List<String>> dp = new ArrayList<>();
        dp.add(Arrays.asList(""));
        
        for (int i = 1; i <= n; i++) {
            List<String> current = new ArrayList<>();
            for (int j = 0; j < i; j++) {
                // 获取前面部分和后面部分并将其两两配对
                List<String> formers = dp.get(j), latters = dp.get(i - j - 1);
                for (String former : formers) {
                    for (String latter : latters) {
                        current.add("(" + former + ")" + latter);
                    }
                }
            }
            dp.add(current);
        }
        
        return dp.get(n);
    }
}
```