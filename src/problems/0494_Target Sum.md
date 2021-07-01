# 494. Target Sum (2021.02.24)

https://leetcode.com/problems/target-sum/

## Solution 1: DFS, brute-force

- $TC:O(2^n)$
- $SC:O(n)$
- 通过DFS探索所有的可能情况，即每个数在构成sum的时候，要不然为正，要不然为负，总共有2^n种情况。
- 但这种方法在递归的过程中会遇到重复求解子问题的情况，比如对于输入[1,1,1,1,1]，我们用(index, curSum)表示每次递归。
- 那么就存在[1,1] -> [2,2], [2,0] -> ...，和[1,-1] -> [2,0], [2,-2] -> ...
- 很明显[2,0]这个递归求解的子问题是重复的，因此我们需要进一步优化该解法。

```java
class Solution {
    public int findTargetSumWays(int[] nums, int S) {
        if (nums == null || nums.length == 0) return 0;
        
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum < S || S < -sum) return 0;
        
        return findTargetSumWays(nums, 0, 0, S);
    }
    
    private int findTargetSumWays(int[] nums, int index, int curSum, int target) {
        if (index == nums.length) {
            if (curSum == target) return 1;
            return 0;
        }
        
        int positive = findTargetSumWays(nums, index + 1, curSum + nums[index], target);
        int negative = findTargetSumWays(nums, index + 1, curSum - nums[index], target);
        return positive + negative;
    }
}
```

## Solution 2: DFS, memorization

- $TC:O(n * sum)$
- $SC:O(n * sum)$
- 由Solution1可知，在递归的过程中存在很多重复解决的子问题。
- 这些子问题可以用(index, curSum)来表示，意思是前index个num的和为curSum的时，对应的最终能满足和为S的组合数量。
- 一旦在首次递归(index, curSum)时求得满足条件的解的个数，我们就可以将其保存起来，在之后的递归中再次遇到这个问题时就可以直接返回对应的解。

```java
class Solution {
    public int findTargetSumWays(int[] nums, int S) {
        if (nums == null || nums.length == 0) return 0;
        
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum < S || S < -sum) return 0;
        
        int n = nums.length;
        // nums的所有可能的curSum为[-sum,sum]，记得把0也算进去
        int[][] memo = new int[n][2 * sum + 1];
        // 由于我们无法用负数的sum作为数组索引，我们将起始值设置为sum，目标和设置为S+sum
        // 相当于把之前的[-sum, sum]映射到[0, 2*sum]去处理
        return findTargetSumWays(nums, 0, sum, memo, S + sum);
    }
    
    private int findTargetSumWays(int[] nums, int index, int curSum, int[][] memo, int target) {
        if (index == nums.length) {
            if (curSum == target) return 1;
            return 0;
        }
        
        // 如果子问题已经被求解过，可以直接返回，不必重复递归下去
        if (memo[index][curSum] > 0) return memo[index][curSum];
        
        int positive = findTargetSumWays(nums, index + 1, curSum + nums[index], memo, target);
        int negative = findTargetSumWays(nums, index + 1, curSum - nums[index], memo, target);

        // 记录子问题的解，即前index个数的和为curSum时有positive + negative种可行解
        memo[index][curSum] = positive + negative;
        return positive + negative;
    }
}
```

## Solution 3: 2-D DP

- $TC:O(n * sum)$
- $SC:O(n * sum)$
- 本题其实是0/1背包问题的一个变形，都有一系列待选择的带权重的物品（nums），一个由选出物品构成的目标（S)。
- 只不过0/1背包问题里每个物品可以选也可以不选，本问题中每个num都必须被用到。
- 因此，我们可以定义dp[i][j]，表示nums的前i个数之和为j时有多少种可能的组合。
- 那么，我们可以得到状态转移方程dp[i][j] = dp[i][j] + dp[i-1][j+nums[i-1]]以及dp[i][j] = dp[i][j] + dp[i-1][j-nums[i-1]]
- 说明只要我们知道nums前(i-1)个数之和为(j+nums[i-1])和(j-nums[i-1])的可能的组合情况，我们就可以推导出nums前i个数之和为j的可能组合情况。
- 需要注意的是(j+nums[i-1])和(j-nums[i-1])要在合理的范围内，且base case为dp[0][sum] = 1（和Solution2一样，sum其实对应着和为0）。
- https://leetcode.com/problems/target-sum/discuss/97335/Short-Java-DP-Solution-with-Explanation (wfei26的解答值得参考)

```java
class Solution {
    public int findTargetSumWays(int[] nums, int S) {
        if (nums == null || nums.length == 0) return 0;
        
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum < S || S < -sum) return 0;
        
        int n = nums.length;
        int[][] dp = new int[n + 1][2 * sum + 1];
        // base case，没有实际的意义，只是i=1的case需要由这么一个case推导出来
        dp[0][sum] = 1;
        
        for (int i = 1; i <= n; i++) {
            // 遍历所有可能的sum，检查j+nums[i-1]和j-nums[i-1]的范围并更新dp[i][j]
            // 不过其实写成dp[i][j - nums[i - 1]] += dp[i - 1][j]和dp[i][j + nums[i - 1]] += dp[i - 1][j]也可以
            for (int j = 0; j <= 2 * sum; j++) {
                if (j - nums[i - 1] >= 0) dp[i][j] += dp[i - 1][j - nums[i - 1]];
                if (j + nums[i - 1] <= 2 * sum) dp[i][j] += dp[i - 1][j + nums[i - 1]];
            }
        }
        
        // 我们想求的就是前n个数（其实就是nums的所有数）之和为S+sum的可能情况
        return dp[n][S + sum];
    }
}
```

## Solution 4: 1-D DP

- $TC:O(n * sum)$
- $SC:O(sum)$
- 上述二维DP还可以进一步优化到一维DP。
- 由Solution3可以发现，我们在求dp[i][x]的时候dp[i-1][y]已经都求出来了，且在i的循环中不会更新i-1的信息。
- 因此我们只需要维护两个一维数组并交换即可。

```java
class Solution {
    public int findTargetSumWays(int[] nums, int S) {
        if (nums == null || nums.length == 0) return 0;
        
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum < S || S < -sum) return 0;
        
        int n = nums.length;
        int[] dp = new int[2 * sum + 1];
        dp[sum] = 1;
        
        for (int i = 1; i <= n; i++) {
            // 下一个index对应的dp
            int[] next = new int[2 * sum + 1];
            
            for (int j = 0; j <= 2 * sum; j++) {
                // 此处只要检查dp[j]是否为0即可
                // 若dp[j]=0，说明之前的数不存在任何一种可能的组合使它们的和为j，在这种情况下计算下去就可能会超出数组的范围
                // 否则，若dp[j]!=0，就可以确定至少存在一种可能的组合可以达到和为j
                // 当然也可以用Solution3中的条件去check：
                //      if (j - nums[i - 1] >= 0) next[j] += dp[j - nums[i - 1]];
                //      if (j + nums[i - 1] <= 2 * sum) next[j] += dp[j + nums[i - 1]];
                if (dp[j] != 0) {
                    next[j - nums[i - 1]] += dp[j];
                    next[j + nums[i - 1]] += dp[j];
                }
            }
            
            dp = next;
        }
        
        return dp[S + sum];
    }
}
```