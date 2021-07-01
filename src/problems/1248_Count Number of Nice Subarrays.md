# 1248. Count Number of Nice Subarrays (2021.04.30)

https://leetcode.com/problems/count-number-of-nice-subarrays/

## Solution 1: Sliding Window - 2 pass

- $TC:O(n)$
- $SC:O(1)$
- atMost(k)可以求出nums中包含至多k个奇数的subarray的数量。
- 因为我们想要的是恰好包含k个奇数的subarray，我们就可以先求出atMost(k)，然后求出atMost(k-1)。
- 最后，atMost(k) - atMost(k-1)就是我们要求的值。

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
    
    private int atMost(int[] nums, int k) {
        int count = 0, n = nums.length, i = 0;
        for (int j = 0; j < n; j++) {
            if (nums[j] % 2 == 1) k--;
            while (k < 0) {
                // window内包含的奇数超过k个，开始右移i缩小范围
                if (nums[i++] % 2 == 1) k++;
            }
            count += (j - i + 1);
        }
        return count;
    }
}
```

## Solution 2: Sliding Window - 1 pass

- $TC:O(n)$
- $SC:O(1)$
- 我们维护一个滑动窗口[i, j]，首先向右移动j直至找到k个奇数。此时，我们就找到了一个合适的子数组，然后右移i直至发现窗口内的第一个奇数。
- 这时候，i移动范围内的元素数量就是满足条件的子数组的数量。
- 比如，对于nums = [2, 2, 1, 2, 1, 2, 2, 1, ...]，i会停留在index = 3的位置，[1, 2, 1]，[2, 1, 2, 1]，[2, 2, 1, 2, 1]均为所求的子数组。
- 但我们需要注意到的是，对于第二个1后出现的每一个偶数元素，仍然可以和之前的count个子数组形成新的满足条件的count个子数组，这一部分是不能忽略的。

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        int count = 0, i = 0, n = nums.length, res = 0;
        for (int j = 0; j < n; j++) {
            if (nums[j] % 2 == 1) {
                // 当发现新的奇数元素时，可以将count重置，待后续计算
                count = 0;
                k--;
            }
            
            while (k == 0) {
                if (nums[i++] % 2 == 1) k++;
                count++;
            }
            res += count;
        }
        return res;
    }
}
```

## Solution 3: Transform to subarrays with sum K

- $TC:O(n)$
- $SC:O(n)$
- 将偶数转换为0，奇数转化为1，那么该问题就等价于求元素和为k的子数组的个数。

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        int curSum = 0, n = nums.length, res = 0;
        int[] prefixSum = new int[n + 1];
        prefixSum[0] = 1;
        
        for (int i = 0; i < n; i++) {
            curSum += nums[i] % 2;
            if (curSum - k >= 0) res += prefixSum[curSum - k];
            prefixSum[curSum]++;
        }
        
        return res;
    }
}
```