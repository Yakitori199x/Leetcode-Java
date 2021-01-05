## 91. Decode Ways (2020.12.31)

https://leetcode.com/problems/decode-ways/

## Solution 1: DP

- $TC:O(N)$
- $SC:O(N)$
- dp[i]表示长度为i的数字字符串所具有的decode方式数。那么存在状态转移方程: 若第i个数字s(i)不为0，那么dp[i] = dp[i] + dp[i-1]，若s(i-1)为1或s(i-1)为2且s(i)小于7（即能够构成10~26），那么dp[i] = dp[i] + dp[i - 2]。可以发现要计算dp[i]，只需要dp[i-1]和dp[i-2]，因此SC可以进一步优化至O(1)。

```java
class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0) return 0;
        
        int n = s.length();
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = s.charAt(0) == '0' ? 0 : 1;
        
        for (int i = 2; i <= n; i++) {
            char cur = s.charAt(i - 1), pre = s.charAt(i - 2);
            if (cur != '0') dp[i] += dp[i - 1];
            if (pre == '1' || (pre == '2' && cur < '7')) dp[i] += dp[i - 2];
        }
        
        return dp[n];
    }
}
```

## Solution 2: Recursion

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0) return 0;
        
        int n = s.length();
        int[] memo = new int[n + 1];
        // 把memo的初始值设定为-1，否则递归时的判断条件就必须是memo[n] > 0
        // 对于有多个初始0的字符串仍然需要重复递归
        for (int i = 0; i < n + 1; i++) memo[i] = -1;
        return numDecodings(s, n, memo);
    }
    
    private int numDecodings(String s, int n, int[] memo) {
        if (n == 0) return 1;
        if (n == 1) return s.charAt(0) == '0' ? 0 : 1;
        if (memo[n] >= 0) return memo[n];
        
        char cur = s.charAt(n - 1), pre = s.charAt(n - 2);
        // 当第一次对memo[n]进行计算时，将默认值更改为0，否则在-1的基础上计算最终结果会少1
        memo[n] = 0;
        if (cur != '0') memo[n] = numDecodings(s, n - 1, memo);
        if (pre == '1' || (pre == '2' && cur < '7')) memo[n] += numDecodings(s, n - 2, memo);
        
        return memo[n];
    }
}
```

