## 53. Maximum Subarray (2020.05.26)

https://leetcode.com/problems/maximum-subarray/

## Solution 1

- $TC:O(n^2)$
- $SC:O(1)$
- Search all subarrays and compare their sum with current maximum

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null) return 0;
        
        int res = nums[0];
        for (int i = 0; i < nums.length; i++) {
            int sum = 0;
            for (int j = i; j < nums.length; j++) {
                sum += nums[j];
                res = Math.max(res, sum);
            }
        }
        
        return res;
    }
}
```

## Solution 2: DP (Kadane's Algorithm)

- $TC:O(n)$
- $SC:O(n)$
- Suppose there is a dp[i], which represents the largest sum of subarray ends with index i
- So dp[i] should be the larger one of: 1. the largest sum of subarray ends with index i - 1 plus nums[i] or 2. nums[i], the value in index i itself (dp[i - 1] may less than 0)
- We can derive the formula dp[i] = max(nums[i], dp[i - 1] + nums[i])

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null) return 0;
        
        int res = nums[0];
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        
        for (int i = 1; i < nums.length; i++) {
            dp[i] = Math.max(nums[i], dp[i - 1] + nums[i]);
            res = Math.max(dp[i], res);
        }
        
        return res;
    }
}
```

## Solution 3: DP, compress the space for dp[] to an integer

- $TC:O(n)$
- $SC:O(1)$
- Because we only need the maximum in dp[] and status in (i) only depends on status in (i-1)

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null) return 0;
        
        int res = nums[0];
        int maxSumEndHere = nums[0];
        
        for (int i = 1; i < nums.length; i++) {
            maxSumEndHere = Math.max(nums[i], maxSumEndHere + nums[i]);
            res = Math.max(maxSumEndHere, res);
        }
        
        return res;
    }
}
```

## Solution 4: Divide and Conquer

- $TC:O(nlogn)$
- $SC:O(logn)$
- Break nums into two halves and find the maximum subarray in each part recursively
- After obtaining lmax and rmax, we need to consider the sum of subarray spans two halves
- Because it must contain nums[mid], we can iterate towards both ends separately
- The third candidate maximum subarray should be nums[mid] + ml + mr

```java
class Solution {
    public int maxSubArray(int[] nums) {
        return maxSubArray(nums, 0, nums.length - 1);
    }
    
    private int maxSubArray(int[] nums, int l, int r) {
        if (l > r) return Integer.MIN_VALUE;
        
        int mid = (r - l) / 2 + l;
        int lmax = maxSubArray(nums, l, mid - 1);
        int rmax = maxSubArray(nums, mid + 1, r);
        
        int sum = 0;
        int mr = 0;
        int ml = 0;
        for (int i = mid + 1; i <= r; i++) {
            sum += nums[i];
            mr = Math.max(mr, sum);
        }
        
        sum = 0;
        for (int i = mid - 1; i >= l; i--) {
            sum += nums[i];
            ml = Math.max(ml, sum);
        }
        
        return Math.max(Math.max(lmax, rmax), nums[mid] + ml + mr);
    }
}
```

