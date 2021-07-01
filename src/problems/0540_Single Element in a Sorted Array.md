# 540. Single Element in a Sorted Array (2021.04.10)

https://leetcode.com/problems/single-element-in-a-sorted-array/

## Solution 1: Binary Search ver1.

- $TC:O(logn)$
- $SC:O(1)$
- 本题的关键在于，我们需要认识到在这个唯一的仅出现一次的target之前，所有的Pair对应的index应该是Pair[0]对应偶数索引，Pair[1]对应奇数索引。
- 而当target出现后（必定出现在偶数索引处），所有的Pair对应的index就改变为Pair[0]对应奇数索引，Pair[1]对应偶数索引。
- 比如nums = [3,3,7,7,10,11,11]，[3,3]和[7,7]都满足第一种规律，而10出现后，[11,11]满足第二种规律。
- 因此，我们可以根据这个规则来改变我们二分搜索的状态空间。
- 即我们每次获取的mid都要是偶数（如果是奇数就减去1，因为lo=0就是偶数，所以不必担心数组越界问题），然后比较nums[mid]和nums[mid + 1]。
- 如果两者相等，说明我们想找的target应该在右半部分，将lo设置为mid+2（因为mid+1和mid构成一对Pair，已经没有探索的价值了）。
- 否则，说明我们想找的target应该在左半部分，将hi设置为mid。

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        // 这里hi如果设置为nums.length，会导致后续的nums[mid] == nums[mid + 1]数组越界（target为最后一个数的情况）
        // 所以这里用的是nums.length - 1
        int lo = 0, hi = nums.length - 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (mid % 2 != 0) mid = mid - 1;
            if (nums[mid] == nums[mid + 1]) lo = mid + 2;
            else hi = mid;
        }
        return nums[lo];
    }
}
```

## Solution 2: Binary Search ver2.

- $TC:O(logn)$
- $SC:O(1)$
- 可以将初始的hi设置为nums长度的一半，然后搜索时使用2*mid来保证mid始终对应的是偶数的index。

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int lo = 0, hi = nums.length / 2;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[2 * mid] == nums[2 * mid + 1]) lo = mid + 1;
            else hi = mid;
        }
        return nums[2 * lo];
    }
}
```