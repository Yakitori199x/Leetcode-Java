## 198. House Robber (2020.11.08)

https://leetcode.com/problems/house-robber/

## Solution 1: DP

- $TC:O(n)$
- $SC:O(n)$
- dp[i]表示在第i间房子可以获取的最大利益。这时候只有两种选择，即抢或是不抢。如果选择抢，那么dp[i]=dp[i-2]+nums[i]（因为不允许抢相邻的房子）。如果选择不抢，那么dp[i]=dp[i-1]。所以，综合一下可以得到dp[i]=max(dp[i-2]+nums[i], dp[i-1])。

```java
class Solution {
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];
        
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        dp[1] = Math.max(dp[0], nums[1]);
        
        for (int i = 2; i < nums.length; i++) {
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        
        return dp[dp.length - 1];
    }
}
```

## Solution 2: DP (optimize space complexity)

- $TC:O(n)$
- $SC:O(1)$
- 可以发现dp的过程只用到了dp[i-1]和dp[i-2]，因此不需要为其维护一个数组，只需要prev1和prev2两个变量进行迭代即可。

```java
class Solution {
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];
        
        int prev1 = 0, prev2 = 0;
        for (int i = 0; i < nums.length; i++) {
            int tmp = Math.max(prev1, prev2 + nums[i]);
            prev2 = prev1;
            prev1 = tmp;
        }
        
        return prev1;
    }
}
```

