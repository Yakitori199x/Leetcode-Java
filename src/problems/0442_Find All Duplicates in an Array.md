# 442. Find All Duplicates in an Array (2021.01.11)

https://leetcode.com/problems/find-all-duplicates-in-an-array/

## Related Questions:
- 448. Find All Numbers Disappeared in an Array

## Solution 1: Negate the value in array

- $TC:O(n)$
- $SC:O(1)$
- 如果某个目标位置的值已经被翻转为负数，则说明当前值重复出现。

```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        if (nums == null) return new ArrayList<>();
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int idx = Math.abs(nums[i]) - 1;
            if (nums[idx] > 0) nums[idx] = -nums[idx];
            else res.add(idx + 1);
        }
        
        return res;
    }
}
```