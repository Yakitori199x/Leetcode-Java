# 1262. Greatest Sum Divisible by Three (2021.05.22)

https://leetcode.com/problems/greatest-sum-divisible-by-three/

## Solution 1: DP ver1

- $TC:O(n)$
- $SC:O(1)$
- 数组的子序列之和mod3后共存在3种不同的结果，分别是余数为0（即我们想求的），余数为1，以及余数为2的情况。
- 我们分别用dp[0]，dp[1]，dp[2]来表示从nums的首元素到当前元素nums[i]的这一段区间内，mod3后得到相应的余数r的子序列最大和。
- 那么新的dp[r]就取决于0到i-1区间对应的旧的dp以及nums[i]。如果当前的nums[i]有(nums[i] + dp[0]) % 3 = 0，那么dp[0] = max(old_dp[0], nums[i] + dp[0])。
- 如果(nums[i] + dp[0]) % 3 = 1，那么就有dp[1] = max(old_dp[1], nums[i] + dp[0])...
- 总结一下就是，将当前的nums[i]与dp[0/1/2]相加并求出余数r，那么dp[r]就可能存在更大的sum -> nums[i] + dp[0/1/2]，最终结果就是旧的dp[r]的值和nums[i] + dp[0/1/2]的较大者。

```java
class Solution {
    public int maxSumDivThree(int[] nums) {
        int n = nums.length;
        int[] dp = new int[3];
        for (int num : nums) {
            int[] temp = Arrays.copyOf(dp, 3);
            for (int r = 0; r < 3; r++) {
                int remainder = (num + temp[r]) % 3;
                // max中的第二个元素不能是temp[remainder]
                // 因为我们dp的初始值都是0，所以可能出现num = 5，temp[0] = 9，temp[1] = 0这种更新了两次dp[2]的情况
                // 而temp[1] = 0的更新是错误的
                dp[remainder] = Math.max(num + temp[r], dp[remainder]);
            }
        }
        return dp[0];
    }
}
```

## Solution 2: DP ver2

- $TC:O(n)$
- $SC:O(1)$
- 还有一种思路就是我们计算nums所有元素的总和res。
- 如果res恰好能被3整除，那么它肯定是能够被3整除的数组的最大和。
- 如果res除以3得到余数1，那么我们就把res减去nums中mod3得到余数为1的最小值（可能是单个元素，也可能是多个元素之和）。
- 如果res除以3得到余数2，同理。
- 因此，该解法的关键就是追踪并记录下nums中mod3余数为1和余数为2的情况对应的最小值。

```java
class Solution {
    public int maxSumDivThree(int[] nums) {
        int res = 0, leastLeftOne = 40000, leastLeftTwo = 40000;
        
        for (int num : nums) {
            res += num;
            if (num % 3 == 1) {
                leastLeftTwo = Math.min(leastLeftTwo, leastLeftOne + num);
                leastLeftOne = Math.min(leastLeftOne, num);
            }
            
            if (num % 3 == 2) {
                leastLeftOne = Math.min(leastLeftOne, leastLeftTwo + num);
                leastLeftTwo = Math.min(leastLeftTwo, num);
            }
        }
        
        if (res % 3 == 0) return res;
        if (res % 3 == 1) return res - leastLeftOne;
        return res - leastLeftTwo;
    }
}
```