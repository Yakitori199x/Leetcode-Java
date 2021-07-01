# 153. Find Minimum in Rotated Sorted Array (2021.04.05)

https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/

## Related Questions:
- 33. Search in Rotated Sorted Array

## Solution 1: Binary Search

- $TC:O(logn)$
- $SC:O(1)$
- 最直接的解法就是遍历数组找到最小值，开销是O(n)，但还有更优的解法。
- 通常来说，比O(n)更快的解法，一般就是O(logn)了，我们很容易想到从二分搜索去切入。
- 但是，现在的nums已经不再有序，我们要怎么对mid进行判断然后决定每次的搜索范围呢？
- 通过简单的观察，我们可以发现rotated sorted array具有这样一种性质。
- 即左半部分，从0到minimum（pivot）的前一个位置是递增的。在pivot处递减，然后在右半部分，从pivot到end又是递增的。而且存在nums[0] > nums[end]。
- 我们可以得出，如果nums[mid] > nums[hi]，说明mid位于左半部分，而我们要找的min肯定在其右侧。
- 同样地，如果nums[mid] < nums[hi]，说明mid位于右半部分，我们要找的min或许就是mid，或许在mid的左侧。
- 虽然hi会随着搜索不断被改变，但是可以确保其不会超过min的位置往左移动，最终会停留在min处。

```java
class Solution {
    public int findMin(int[] nums) {
        int lo = 0, hi = nums.length - 1;
        // 关于什么时候用lo < hi，什么时候用lo <= hi
        // (1) 一般来说，如果hi对应的是待搜索的最后一个元素的下一位，比如nums.length
        //     说明hi所对应的是已经被判断过或者是已经不需要进行判断的数，那么可以用lo < hi配上hi = mid
        // (2) 如果hi正好对应待搜索的最后一个元素，比如nums.length - 1，说明hi要保持指向尚未被判断的最后一个数
        //     那么可以用lo <= hi搭配hi = mid - 1
        // (3) 当然，这里的情况比较特殊，使用了lo < hi搭配hi = mid，好像与我们阐述的(1)，(2)不相符
        //     但其实lo < hi或lo <= hi，根本在于lo == hi的情况会不会进行判定
        //     如果我们要找的是某个确定的target是否在nums中，也就是说target可能是不存在的，那么一定要判断lo == hi的情况
        //     就这题的背景来看，我们要找的min必定在nums中，因此lo == hi的情况不判断也不影响最终结果，我们的目的就是让lo和hi收敛到一个位置即可
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] > nums[hi]) lo = mid + 1;
            else hi = mid;
        }
        return nums[lo];
    }
}
```