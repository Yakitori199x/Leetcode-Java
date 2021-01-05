## 16. 3Sum Closest (2020.07.21)

https://leetcode.com/problems/3sum-closest/

## Solution 1: Two pointers

- $TC:O(N^2)$
- $SC:O(1)$
- Iterate through the array and fix leftmost pointer i
- Do two pointers 2-Sum against to the part from i + 1
- Check the difference between sum of current three elements and target during 2 pointers
- Set it as the result if it is closer to the target

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int res = nums[0] + nums[1] + nums[nums.length - 1];
        
        for (int i = 0; i < nums.length - 2; i++) {
            if (i != 0 && nums[i] == nums[i - 1]) continue;
            
            int l = i + 1, r = nums.length - 1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (sum == target) {
                    return target;
                } else if (sum < target) {
                    l++;
                } else {
                    r--;
                }
                
                if (Math.abs(target - sum) < Math.abs(target - res)) res = sum;
            }
        }
        
        return res;
    }
}
```

## Solution 2: Binary Search

- $TC:O(N^2logN)$, $N^2$ for iteration, $logN$ for binary search
- $SC:O(1)$
- Use a nested interation with i and j
- Fix i, j and do binary search against left part of nums -> (j + 1 ~ nums.length - 1)
- If we find the complement, the diff can be 0, return target directly
- If not, we should check the difference between the next higher value and complement, and the difference between the previous lower value and complement

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int diff = Integer.MAX_VALUE;
        
        for (int i = 0; i < nums.length - 2; i++) {
            if (i != 0 && nums[i] == nums[i - 1]) continue;
            
            for (int j = i + 1; j < nums.length - 1; j++) {
                if (j != i + 1 && nums[j] == nums[j - 1]) continue;
                
                int complement = target - nums[i] - nums[j];
                int idx = Arrays.binarySearch(nums, j + 1, nums.length - 1, complement);
                
                if (idx >= 0) {
                    return target;
                } else {
                    int hi = -idx - 1;
                    int lo = hi == j + 1 ? j + 1 : hi - 1;
                    if (Math.abs(nums[hi] - complement) < Math.abs(diff)) diff = complement - nums[hi];
                    if (Math.abs(nums[lo] - complement) < Math.abs(diff)) diff = complement - nums[lo];
                }
            }
        }
        
        return target - diff;
    }
}
```

