# 45. Jump Game II (2021.03.05)

https://leetcode.com/problems/jump-game-ii/

## Solution 1: DP

- $TC:O(n^2)$
- $SC:O(n)$
- 本题比较容易想到的一个解法就是DP。
- dp[i]表示到达i的最小步数，dp[0]=0。
- 状态转移方程为dp[i] = min(dp[i], dp[j]+1 if (nums[j] + j) >= i) (0 <= j < i)。
- 不过这种解法消耗的时间复杂度为O(n^2)。

```java
class Solution {
    public int jump(int[] nums) {
        int[] dp = new int[nums.length];
        // 注意初始值不能为0，因为要求的是min
        for (int i = 0; i < nums.length; i++) dp[i] = Integer.MAX_VALUE;
        
        dp[0] = 0;
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                dp[i] = Math.min(dp[i], (nums[j] + j) >= i ? dp[j] + 1 : Integer.MAX_VALUE);
            }
        }
        return dp[nums.length - 1];
    }
}
```

## Solution 2: BFS-like

- $TC:O(n)$
- $SC:O(1)$
- 这种思路有点像BFS的层次遍历。
- 以nums=[2, 3, 1, 1, 4]为例，初始状态下我们位于nums[0] = 2。
- 从nums[0]开始最远可以移动到nums[2]，但并不是说就直接移动到当前最远位置nums[2]。
- 因为有可能先移动到nums[1]，再基于nums[1]移动可以用更少的步数到达终点（或者说移动得更远）。
- 所以，我们要探索nums[1]和nums[2]（即最少用一步能到达的范围内的所有结点），看一看第二步可以到达的最远位置。
- 以此类推，直到发现n-1处于能到达的最远位置的范围内。

```java
class Solution {
    public int jump(int[] nums) {
        int steps = 0, start = 0, end = 0, n = nums.length;
        while (end < n - 1) {
            // 如果存在不能到达的情况，则将maxEnd设置为end进行判断
            // 因为不能到达的话，就会存在更新后的maxEnd无法超过end的情况
            // 不过本题不需要考虑该情况
            int maxEnd = end + 1;
            steps++;
            for (int i = start; i <= end; i++) {
                // 如果可到达的最远位置包含了n-1，直接返回
                if (nums[i] + i >= n - 1) return steps;
                maxEnd = Math.max(maxEnd, nums[i] + i);
            }

            // 设置下一轮遍历的起始位置
            start = end + 1;
            end = maxEnd;
        }
        return steps;
    }
}
```

## Solution 3: Greedy

- $TC:O(n)$
- $SC:O(1)$
- 贪心算法的关键在于每一次都选取最能够接近目标的操作。
- 本题的目标是以最少的steps到达终点（n-1），也就是说，我们在每一次都要选出能够跳得最远的操作。
- 换句话说，就是在每一次跳跃可到达的范围内选取出下一次跳跃的可到达的最远范围。
- 比如，第一跳我们位于nums[0]，没有其他选择的余地，只能从nums[0]起跳。
- 之后我们就可以在第一跳可到达范围[1,end]内探索第二跳能够到达的最远位置，以此类推。
- 其实整体流程类似Solution2的BFS，但是从Greedy的角度切入思考。

```java
class Solution {
    public int jump(int[] nums) {
        int steps = 0, end = 0, maxEnd = 0, n = nums.length;
        for (int i = 0; i < n - 1; i++) {
            maxEnd = Math.max(nums[i] + i, maxEnd);
            // end用来标记这个step的探索范围
            if (i == end) {
                steps++;
                end = maxEnd;
            } 
        }
        return steps;
    }
}
```