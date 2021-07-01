# 81. Search in Rotated Sorted Array II (2021.04.06)

https://leetcode.com/problems/search-in-rotated-sorted-array-ii/

## Related Questions:
- 33. Search in Rotated Sorted Array

## Solution 1: Binary Search ver1.

- $TC:O(n)$，worst case下为O(n)，即所有元素都相等的时候，每次只能缩小两格的搜索范围。average case下为O(logn)。
- $SC:O(1)$
- 本题和LC33类似，唯一的不同就在于nums现在包含了duplicates，或者说nums中有相等的数存在。
- 但实际上就算有相等的数存在，大部分情况下还是可以用LC33中的解法对该题进行求解。
- 比如nums[lo] = nums[mid]且nums[lo] > nums[hi]的情况，或者是nums[lo] < nums[mid]且nums[lo] = nums[hi]的情况。
- 唯一会使我们无法通过mid更新下一轮循环的搜索范围的情况就是nums[lo] = nums[mid] = nums[hi]，因为我们无法断定mid究竟位于左侧还是右侧。
- 所以，与LC33实现的不同就在于，我们在nums[lo] = nums[mid] = nums[hi]的情况下，不能直接根据mid把搜索范围缩小一半。
- 而是只能把lo和hi处的元素排除（因为之前判断过nums[mid] == target）。

```java
class Solution {
    public boolean search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n - 1;
        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] == target) return true;
            if (nums[mid] == nums[lo] && nums[mid] == nums[hi]) {
                hi--;
                lo++;
            } else if (nums[lo] <= nums[mid]) {
                if (nums[lo] <= target && target < nums[mid]) hi = mid - 1;
                else lo = mid + 1;
            } else {
                if (nums[mid] < target && target <= nums[hi]) lo = mid + 1;
                else hi = mid - 1;
            }
        }
        return false;
    }
}
```

## Solution 2: Binary Search ver1.

- $TC:O(n)$，worst case下为O(n)，即所有元素都相等的时候，每次只能缩小一格的搜索范围。average case下为O(logn)。
- $SC:O(1)$
- 相比于Solution1更加严格一些，只要满足nums[mid] == nums[lo]就采取比较保守的更新策略，将左边界往右移动一格。

```java
class Solution {
    public boolean search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n - 1;
        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] == target) return true;
            if (nums[mid] == nums[lo]) {
                lo++;
            } else if (nums[lo] <= nums[mid]) {
                if (nums[lo] <= target && target < nums[mid]) hi = mid - 1;
                else lo = mid + 1;
            } else {
                if (nums[mid] < target && target <= nums[hi]) lo = mid + 1;
                else hi = mid - 1;
            }
        }
        return false;
    }
}
```

## Solution 3: Binary Search ver1.

- $TC:O(n)$，worst case下为O(n)，average case下为O(logn)。
- $SC:O(1)$
- 在每次进行二分搜索前，先对lo和hi两侧进行去重，保证最后的搜索范围内不构成nums[lo] = nums[mid] = nums[hi]。
- 且去重不影响我们确定target是否存在于nums中。

```java
class Solution {
    public boolean search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n - 1;
        while (lo <= hi) {
            while (lo < n - 1 && nums[lo] == nums[lo + 1]) lo++;
            while (hi > 0 && nums[hi] == nums[hi - 1]) hi--;
            
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] == target) return true;
            if (nums[lo] <= nums[mid]) {
                if (nums[lo] <= target && target < nums[mid]) hi = mid - 1;
                else lo = mid + 1;
            } else {
                if (nums[mid] < target && target <= nums[hi]) lo = mid + 1;
                else hi = mid - 1;
            }
        }
        return false;
    }
}
```