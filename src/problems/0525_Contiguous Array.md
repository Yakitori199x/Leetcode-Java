# 525. Contiguous Array (2021.03.19)

https://leetcode.com/problems/contiguous-array/

## Solution 1: PrefixSum + HashMap

- $TC:O(n)$
- $SC:O(n)$
- 本题所求的是包含相等数量的0和1的最长子数组。
- 我们可以把0看成-1，1还是1，那么目标就转换为求子数组之和为0的最长子数组。
- Naive的解法就是探索所有子数组，然后把sum为0的最长子数组的长度返回。
- 当然，我们可以很容易地想到利用前缀和来求得子数组的sum。并且利用HashMap进步一优化到O(n)的时间复杂度。

```java
class Solution {
    public int findMaxLength(int[] nums) {
        Map<Integer, Integer> seen = new HashMap<>();
        int prefixSum = 0, res = 0;
        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i] == 0 ? -1 : 1;
            if (prefixSum == 0) {
                // 如果0到i的前缀和为0，那么这一段肯定是当前符合条件的最长子数组
                res = i + 1;
            } else {
                // 保存该前缀和第一次出现的位置
                // 由于我们的目标是子数组的sum为0，那么对于当前的prefixSum，我们就要找到第一次出现的prefixSum的位置
                // 因为prefixSum - prefixSum = 0
                seen.putIfAbsent(prefixSum, i);
                if (seen.containsKey(prefixSum)) {
                    res = Math.max(res, i - seen.get(prefixSum));
                }
            }
        }
        return res;
    }
}
```

## Solution 2: PrefixSum + HashMap ver2

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int findMaxLength(int[] nums) {
        Map<Integer, Integer> seen = new HashMap<>();
        int prefixSum = 0, res = 0;
        // 解法一的一个变形，将目标prefixSum = 0首次出现的目标设定为-1
        // 这样在遍历的时候就不需要特别去判断prefixSum是否为0
        // 直接到map中查找即可，若prefixSum为0，则结果就是i-(-1) = i+1
        seen.put(0, -1);
        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i] == 0 ? -1 : 1;
            seen.putIfAbsent(prefixSum, i);
            if (seen.containsKey(prefixSum)) {
                res = Math.max(res, i - seen.get(prefixSum));
            }
        }
        return res;
    }
}
```