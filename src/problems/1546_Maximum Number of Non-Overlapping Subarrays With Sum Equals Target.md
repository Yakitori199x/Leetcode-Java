# 1546. Maximum Number of Non-Overlapping Subarrays With Sum Equals Target (2021.05.26)

https://leetcode.com/problems/maximum-number-of-non-overlapping-subarrays-with-sum-equals-target/

## Solution 1: DP ver1.

- $TC:O(n^2)$
- $SC:O(n)$
- dp[i]表示nums[0...i-1]所拥有的和为target且不重叠的子数组的最大数量。
- 我们只需要从当前的j=i开始，往回移动j从而计算出nums[j...i]的子数组和sum。
- 那么就有状态转移方程：dp[i] = max(dp[i], dp[j - 1] + sum == target ? 1 : 0)。
- 由于该解法的时间复杂度为n的平方，在该题的环境下会超时，需要更优的解法。

```java
class Solution {
    public int maxNonOverlapping(int[] nums, int target) {
        int n = nums.length;
        int[] dp = new int[n + 1];
        for (int i = 0; i < n; i++) {
            int sum = 0;
            for (int j = i; j >= 0; j--) {
                sum += nums[j];
                // 这里将i对应存储到dp[i + 1]的位置，否则i = 0时，j - 1会变成负数
                dp[i + 1] = Math.max(dp[i + 1], dp[j] + (sum == target ? 1 : 0));
            }
        }
        return dp[n];
    }
}
```

## Solution 2: DP ver2.

- $TC:O(n)$
- $SC:O(n)$
- 不难发现，对于Solution1，我们不需要把j从i一直探索到0，我们需要的只是第一个（最靠右）遇到的能够使nums[j...i]的子数组和等于target的情况。
- 为什么呢？因为要使不重叠的子数组的数量最大，就最好让每个满足条件的子数组尽可能的短，越靠近数组开头的j，其对应的能够满足条件的子数组nums[j...i]就会占用更多的长度，而且nums[0...j-1]会更短，更可能包含更少数量的满足条件子数组（贪心）。
- 因此，我们只需要遍历一次nums，遍历的同时记录下prefixSum对应的res。
- 如果在新的i处找到了map中存在sum - target的前缀和，说明找到了离i最近的一段和为target的子数组，则设定res为当前res和map.get(sum - target) + 1的较大者。

```java
class Solution {
    public int maxNonOverlapping(int[] nums, int target) {
        int n = nums.length, res = 0, sum = 0;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 0);
        for (int i = 0; i < n; i++) {
            sum += nums[i];
            if (map.containsKey(sum - target)) {
                res = Math.max(res, map.get(sum - target) + 1);
            }
            map.put(sum, res);
        }
        return res;
    }
}
```