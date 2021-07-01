# 1144. Decrease Elements To Make Array Zigzag (2021.05.25)

https://leetcode.com/problems/decrease-elements-to-make-array-zigzag/

## Solution 1: Even or Odd

- $TC:O(n)$
- $SC:O(1)$
- 因为我们只能对减少nums的元素从而使nums达到zigzag的状态，所以只存在两类情况能够使所需的moves最少。
- 一种是使偶数索引的元素始终比两侧的元素小，另一种就是使奇数索引的元素始终比两侧的元素小。（如果既存在偶数索引的元素较小的情况，又存在奇数索引的元素较小的情况，那么nums必然不是zigzag的）
- 所以我们要做的就是对于nums[i]，将其减少到比两侧元素中较小的一个还要小1的值。当然，nums[i]可能原本比两侧元素来得小，那么就不需要move。
- 同时，把nums[i]对应需要的moves累积到res[0]（偶数）或res[1]（奇数）中。
- 如果本题允许的操作是只能够增加nums[i]，那么我们就反过来考虑如何保证偶数/奇数索引的元素始终比两侧的元素大的情况。

```java
class Solution {
    public int movesToMakeZigzag(int[] nums) {
        int n = nums.length;
        int[] res = new int[2];
        for (int i = 0; i < n; i++) {
            // 边界情况不需要比较，直接设置成最大的int
            int left = i == 0 ? Integer.MAX_VALUE : nums[i - 1];
            int right = i == n - 1 ? Integer.MAX_VALUE : nums[i + 1];
            // 需要把nums[i]减小到比两侧元素的较小者更小的程度
            res[i % 2] += Math.max(0, nums[i] - Math.min(left, right) + 1);
        }
        return Math.min(res[0], res[1]);
    }
}
```