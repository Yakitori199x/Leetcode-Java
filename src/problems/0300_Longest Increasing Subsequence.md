# 300. Longest Increasing Subsequence (2021.04.01)

https://leetcode.com/problems/longest-increasing-subsequence/

## Solution 1: Naive

- $TC:O(2^n)$
- $SC:O(n)$
- 最最简单的思想就是将所有的子序列枚举出来，然后判断每个子序列是否是递增的，最后返回最长的递增子序列，但这样需要花费O(n*2^n)。
- 所以我们会想，是否可以只枚举出所有的递增子序列，那么这样就不需要去花费O(n)判断其递增性质。
- 要做到这一点，我们可以递归进行求解。lengthOfLIS(nums, prev, curIndex)求解的是当前位置处于curIndex且前置值为prev的情况下的最长递增子序列。
- 对于每个位置curIndex，我们可以判断其是否大于prev，若大于则说明可以通过加入nums[curIndex]获得更长的递增子序列。
- 当然，即使nums[curIndex]大于prev，我们也可以选择不使用nums[curIndex]去构成子序列。最后返回两种情况的较大者。
- 但如果nums[curIndex]小于等于prev，那curIndex这个位置自然不能被选择，参与构成递增子序列。

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        return lengthOfLIS(nums, Integer.MIN_VALUE, 0);
    }
    
    private int lengthOfLIS(int[] nums, int prev, int curIndex) {
        if (curIndex == nums.length) return 0;
        
        int taken = 0, notTaken = 0;
        if (nums[curIndex] > prev) {
            taken = 1 + lengthOfLIS(nums, nums[curIndex], curIndex + 1);
        }
        notTaken = lengthOfLIS(nums, prev, curIndex + 1);
        return Math.max(taken, notTaken);
    }
}
```

## Solution 2: Memorization

- $TC:O(n^2)$
- $SC:O(n^2)$
- Solution1中很明显存在大量的重复计算，因此我们可以用prev的index作为参数进行递归，同时维护一个memo来记录已经计算过的结果。

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[][] memo = new int[n][n];
        for (int[] m : memo) Arrays.fill(m, -1);
        return lengthOfLIS(nums, -1, 0, memo);
    }
    
    private int lengthOfLIS(int[] nums, int prevIndex, int curIndex, int[][] memo) {
        if (curIndex == nums.length) return 0;
        // 因为prevIndex的初始值是-1，所以对memo操作时prevIndex处要先加上1
        if (memo[prevIndex + 1][curIndex] >= 0) return memo[prevIndex + 1][curIndex];
        
        int taken = 0, notTaken = 0;
        if (prevIndex == -1 || nums[curIndex] > nums[prevIndex]) {
            taken = 1 + lengthOfLIS(nums, curIndex, curIndex + 1, memo);
        }
        notTaken = lengthOfLIS(nums, prevIndex, curIndex + 1, memo);
        memo[prevIndex + 1][curIndex] = Math.max(taken, notTaken);
        return memo[prevIndex + 1][curIndex];
    }
}
```

## Solution 3: DP

- $TC:O(n^2)$
- $SC:O(n)$
- 除了像Solution1和2那样，围绕着每个元素的选与不选进行思考，我们还可以从另外的角度切入。
- 在此，我们定义状态dp[i]表示以nums[i]为结尾的最长子序列的长度。（注意，这里nums[i]不再是可选可不选的，而是子序列必须以它来结尾）。
- 那么我们可以得到状态转移方程dp[i] = max(dp[j] + 1)，其中0 <= j < i且要满足nums[i] > nums[j]。
- 即以nums[i]为结尾的最长子序列的长度会等于以其前方的元素nums[j]为结尾的最长子序列的长度中最大的那个加上1。
- 我们最终要求的答案就是dp中最大的元素。

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        
        // 一个数就是一个长度为1的递增子序列
        Arrays.fill(dp, 1);
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
        
        int res = 0;
        for (int candidate : dp) res = Math.max(res, candidate);
        return res;
    }
}
```

## Solution 4: Greedy + Binary Search

- $TC:O(nlogn)$
- $SC:O(n)$
- 我们需要先明确一个事实，即对于长度为k的递增子序列，其结尾的数越小，越有利于我们在后续遍历中获取到更长的递增子序列。（Greedy）
- 因此，我们可以定义状态tails[i]，记录长度为i+1的递增子序列的结尾的数，且tails是一个严格的单调递增数组（证明见下方链接的题解）。
- 既然tails是严格递增数组，是一个有序的数组，我们就很容易想到是不是可以应用二分搜索。
- 我们基于Greedy的事实，一边遍历nums，一边构建tails。对于每一个数nums[i]，我们先用二分搜索找到当前tails中首个大于或等于nums[i]的位置。
- 如果这个位置已经达到了当前tails的size（不是tails数组的长度，而是当前已知的递增子序列的最大长度），说明nums[i]大于当前已知的所有递增子序列的结尾值，可以构成更长的子序列。否则，说明对应位置的递增子序列可能有更小的结尾值，将tails[hi]设置为nums[i]。
- https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/dong-tai-gui-hua-er-fen-cha-zhao-tan-xin-suan-fa-p/

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] tails = new int[nums.length];
        int size = 0;
        for (int i = 0; i < nums.length; i++) {
            int lo = 0, hi = size;
            while (lo < hi) {
                int mid = (hi - lo) / 2 + lo;
                if (nums[i] > tails[mid]) lo = mid + 1;
                else hi = mid;
            }
            
            if (hi == size) tails[size++] = nums[i];
            else tails[hi] = nums[i];
        }
        return size;
    }
}
```