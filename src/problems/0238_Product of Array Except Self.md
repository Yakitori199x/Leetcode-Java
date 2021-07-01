# 238. Product of Array Except Self (2021.03.06)

https://leetcode.com/problems/product-of-array-except-self/

## Solution 1: Iterative, prefix and suffix product

- $TC:O(n)$
- $SC:O(1)$
- 本题的naive思路就是利用一个双重循环，标定nums[i]后再次循环求得除了nums[i]之外所有数的乘积。
- 但是这种方法需要消耗O(n^2)的时间复杂度。
- 还有一种思路就是把所有数的乘积prod求出来，那么最终答案就是res[i]=prod/nums[i]。（需要注意对nums中0的处理）。
- 不过除法在本题被禁止使用，我们需要另寻他法。
- 其实，本题的突破口在于求nums[i]的前缀积（nums[i]左侧所有数的乘积）和后缀积（nums[i]右侧所有数的乘积）。
- 最终我们想要的答案就是nums[i]的前缀积和后缀积的乘积。
- 我们可以分别将前缀积和后缀积保存在两个数组中，但这里说明的是O(1)空间复杂度的解法。
- 首先，我们从左向右遍历数组，求出每个nums[i]的前缀积并保存在res数组中。
- 然后，我们从右向左遍历数组，求出每个nums[i]的后缀积并保存在一个临时变量right中。
- 同时，每更新一次right，我们就要把right和对应的res[i]（此时为nums[i]前缀积）相乘，求出最终答案。

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        if (nums == null || nums.length < 2) return new int[0];
        
        int n = nums.length;
        int[] res = new int[n];
        // nums[0]的前缀积为1
        res[0] = 1;
        
        // 第一次遍历求前缀积
        for (int i = 1; i < n; i++) {
            res[i] = res[i - 1] * nums[i - 1];
        }
        
        // nums[n - 1]的后缀积为1
        // 第二次遍历，一边求后缀积right，一边更新res到最终答案
        int right = 1;
        for (int j = n - 1; j >= 0; j--) {
            res[j] *= right;
            right *= nums[j];
        }
        
        return res;
    }
}
```