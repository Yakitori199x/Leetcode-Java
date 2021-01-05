## 209. Minimum Size Subarray Sum (2020.08.16)

https://leetcode.com/problems/minimum-size-subarray-sum/

## Solution 1: Two pointers (Sliding window)

- $TC:O(N)$
- $SC:O(1)$
- Maintain two pointers i & j to record the sum of contiguous subarray.
- For each iteration, we add nums[j] and increase j.
- Then we check if current sum >= s, if it is, calculate the minimum length and remove nums[i] from accumulative sum until the sum < s

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int i = 0, j = 0, sum = 0;
        int res = Integer.MAX_VALUE;
        
        while (j < nums.length) {
            sum += nums[j++];
            
            while (sum >= s) {
                res = Math.min(res, j - i);
                sum -= nums[i++];
            }
        }
        
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

## Solution 2: Binary Search

- $TC:O(NlogN)$
- $SC:O(N)$
- 这个方法的关键点在于binary search需要作用于有序序列，但是对于这题的nums的顺序我们并不能随意去更改
- 因此，我们可以构造一个accumulative sum的序列sum，sum[i]代表了nums的前i项之和，然后我们就可以遍历sum并用binary search对其搜索s+sum[i]
- 搜索的结果k存在这么一个关系：s + sum[i] <= sum[k] -> s <= sum[k] - sum[i] (num[i] ... num[k-1]之和，即subarray之和)
- 这种情况下我们就可以更新我们要求的最小长度，若返回的idx等于sum的长度，则说明已经不存在某个和满足大于等于s + sum[i]，可以退出循环

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int res = Integer.MAX_VALUE;
        // sum[0] = 0
        int[] sum = new int[nums.length + 1];
        for (int i = 1; i < sum.length; i++) sum[i] = sum[i - 1] + nums[i - 1];
        
        // 此处需要从0开始遍历，否则会遗漏s <= sum[i]的情况
        for (int i = 0; i < sum.length; i++) {
            int idx = Arrays.binarySearch(sum, s + sum[i]);
            if (idx < 0) idx = - idx - 1;
            if (idx == sum.length) break; 
            res = Math.min(res, idx - i);
        }
        
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

