# 41. First Missing Positive (2021.01.11)

https://leetcode.com/problems/first-missing-positive/

## Related Questions:
- 448. Find All Numbers Disappeared in an Array

## Solution 1: Swap

- $TC:O(n)$
- $SC:O(1)$
- 虽然本题中nums的值不再被限制在1到nums.length之间，但是由于我们只需要找出第一个缺失的正数，说明我们可以忽略非正数以及超过数组长度的数。
- 比如所有数都不在[1,nums.length]的范围内的话，那么First Missing Positive就是nums.length+1。
- 只需要对[1,nums.length]的数进行跟Leetcode.448类似的操作即可。

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        if (nums == null) return 1;
        
        int len = nums.length;
        
        for (int i = 0; i < len; i++) {
            while (nums[i] > 0 && nums[i] <= len && nums[i] != nums[nums[i] - 1]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }
        
        for (int i = 0; i < len; i++) {
            if (nums[i] != i + 1) return i + 1;
        }
        
        return len + 1;
    }
}
```

## Solution 2: Negate

- $TC:O(n)$
- $SC:O(1)$
- 由于nums中有可能存在负数，所以不能简单地进行负数翻转操作。
- 首先需要将nums中不符合[1,nums.length]的数都赋予一个特定的值来标记该值无效，比如n+1。
- 之后的操作就与一般的负数翻转类似，需要注意的是值为n+1的位置也可能被翻转为负，所以不能单纯凭借(nums[i] < n)去判断，否则会出现数组越界。

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        if (nums == null) return 1;
        
        int len = nums.length;
        
        for (int i = 0; i < len; i++) {
            if (nums[i] <= 0 || nums[i] > len) nums[i] = len + 1;
        }
        
        for (int i = 0; i < len; i++) {
            int idx = Math.abs(nums[i]) - 1;
            if (idx < len) {
                nums[idx] = (nums[idx] < 0) ? nums[idx] : -nums[idx];
            }
        }
        
        for (int i = 0; i < len; i++) {
            if (nums[i] >= 0) return i + 1;
        }
        
        return len + 1;
    }
}
```