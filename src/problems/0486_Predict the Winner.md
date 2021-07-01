# 486. Predict the Winner (2021.05.11)

https://leetcode.com/problems/predict-the-winner/

## Solution 1: Recursion (Brute-force) + Turn

- $TC:O(2^n)$
- $SC:O(n)$
- 首先我们需要明确一点，本题的目的并不是求出玩家1能够取得的最大分数是否能超过玩家2。因为本题是基于这样一个前提，即所有玩家都会进行对自己最优的选择，那么很可能玩家1压根无法取得计算出来的最大分数。实际上，本题要判断的就是在游戏结束时，玩家1的分数是否能比玩家2的分数多，即两者的分差能否大于或等于0（因为等于的情况下也是玩家1获胜）。
- 那么要如何进行求解呢？不难发现，当玩家1能够对nums[s,e]这个范围进行操作时，一种选择就是nums[s]，另一种选择就是nums[e]。当然，我们不能简单地选择nums[s]和nums[e]中的较大者，比如nums = [1,5,233,7]的情况。相对地，要让玩家1做出的选择最优，我们就要根据当前步骤的选择以及预测得到的下一步中玩家2进行最优选择的结果来进行判断。
- 我们使用winner(nums, s, e, turn)来表示当前玩家在对nums[s,e]进行最优选择后，在游戏结束时与另一名玩家的分差。其中，turn为1时表示玩家1的回合，turn为-1时表示玩家2的回合，且规定玩家1获取的分数为正，玩家2获取的分数为负。因此，在玩家1的回合中，winner方法要尽可能返回两个结果的较大者，在玩家2的回合中，winner方法要尽可能返回两个结果的较小者（较小值为负数说明玩家2领先玩家1，较小值为正数说明尽可能减少被玩家1拉开的差距）。综上可得出以下的递归实现。

```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        return winner(nums, 0, nums.length - 1, 1) >= 0;
    }
    
    private int winner(int[] nums, int start, int end, int turn) {
        // 这里不同玩家每一轮取得的分数受到turn的影响
        if (start == end) return turn * nums[start];
        
        int scoreA = turn * nums[start] + winner(nums, start + 1, end, -turn);
        int scoreB = turn * nums[end] + winner(nums, start, end - 1, -turn);
        // 对于玩家1来说，每轮取得的分数为正数，在考虑到下一轮玩家2做出最优选择导致的最终游戏分差的情况下，最终结果越大越好
        // 对于玩家2来说，每轮取得的分数为负数，在考虑到下一轮玩家1做出最优选择导致的最终游戏分差的情况下，最终结果越小越好
        return turn == 1 ? Math.max(scoreA, scoreB) : Math.min(scoreA, scoreB);
    }
}
```

## Solution 2: Recursion (Brute-force)

- $TC:O(2^n)$
- $SC:O(n)$
- Solution1中使用了一个turn来区别两个玩家的轮次以及winner方法的返回值对应表示的含义。但其实我们不需要turn也可以实现，且更加方便简洁。
- 在去除turn后，winner(nums, s, e)仍然表示当前玩家在对nums[s,e]进行最优选择后，在游戏结束时与另一名玩家的分差。但是对玩家1和玩家2来说，其意义是一致的，即分差大于0时，当前玩家领先，分差小于0时，当前玩家落后，不受turn的影响。

```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        return winner(nums, 0, nums.length - 1) >= 0;
    }
    
    private int winner(int[] nums, int start, int end) {
        if (start == end) return nums[start];
        
        // 最优选择为1. 获取start的值，扣除下一轮玩家在start + 1到end做出最优选择导致的最终游戏分差 
        // 以及2. 获取end的值，扣除下一轮玩家在start到end - 1做出最优选择导致的最终游戏分差
        // 两者中的较大者
        int scoreA = nums[start] - winner(nums, start + 1, end);
        int scoreB = nums[end] - winner(nums, start, end - 1);
        return Math.max(scoreA, scoreB);
    }
}
```

## Solution 3: Memorization

- $TC:O(n^2)$
- $SC:O(n^2)$
- Solution1和Solution2的计算过程中包含了很多重复的递归计算，我们可以引入memo来优化时间开销。memo为一个n*n的数组，每次递归最多填满memo的一个位置，因此时间复杂度为O(n^2)。

```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int n = nums.length;
        Integer[][] memo = new Integer[n][n];
        return winner(nums, 0, n - 1, memo) >= 0;
    }
    
    private int winner(int[] nums, int start, int end, Integer[][] memo) {
        if (start == end) return nums[start];
        if (memo[start][end] != null) return memo[start][end];
        
        int scoreA = nums[start] - winner(nums, start + 1, end, memo);
        int scoreB = nums[end] - winner(nums, start, end - 1, memo);
        memo[start][end] = Math.max(scoreA, scoreB);
        return memo[start][end];
    }
}
```

## Solution 4: DP

- $TC:O(n^2)$
- $SC:O(n^2)$
- 使用DP也可以求解本题，dp[i][j]即表示当前玩家在nums[i,j]进行最优行动后和对手的分数差。
- 那么状态转移方程为：dp[i][j] = max(nums[i] - dp[i + 1][j], nums[j] - dp[i][j - 1])。

```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int n = nums.length;
        // 这里的意思是当nums长度为偶数时，玩家1（先手）一定会获胜
        // 因为若nums的长度为偶数，那么必然存在sum(偶数索引的元素)>=sum(奇数索引的元素)，或者是sum(奇数索引的元素)>sum(偶数索引的元素)
        // 对于第一种情况来说，玩家1只要在一开始选择了第0个元素，那么玩家2只能在1和n-1这两个奇数索引的元素中进行选择，且这个规律会一直持续下去直到游戏结束
        // 同理，对于第二种情况来说，玩家1只要在一开始选择最后一个元素即可获胜
        if (n % 2 == 0) return true;
        int[][] dp = new int[n][n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (i == j) dp[i][i] = nums[i];
                else dp[i][j] = Math.max(nums[i] - dp[i + 1][j], nums[j] - dp[i][j - 1]);
            }
        }
        return dp[0][n - 1] >= 0;
    }
}
```

## Solution 5: Optimized DP

- $TC:O(n^2)$
- $SC:O(n)$
- 不难发现，Solution4中的dp[i][j]的值只取决于其左侧元素和其下方元素，因此可以把空间压缩到一个一维数组。

```java
class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int n = nums.length;
        if (n % 2 == 0) return true;
        int[] dp = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (i == j) dp[j] = nums[j];
                else dp[j] = Math.max(nums[i] - dp[j], nums[j] - dp[j - 1]);
            }
        }
        return dp[n - 1] >= 0;
    }
}
```