# 34. Find First and Last Position of Element in Sorted Array (2021.04.10)

https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/

## Solution 1: 2-pass

- $TC:O(logn)$
- $SC:O(1)$
- 第一遍二分搜索找出target第一次出现的位置lo，如果target不在nums中则直接返回。（通过lo == nums.length || nums[lo] != target判断）
- 第二遍二分搜索找出target最后一次出现的位置lo-1（注意决定下一次搜索范围的判断条件的变化）。
- 因为这时候已经可以确定target存在于nums中，直接赋值给res即可。

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if (nums.length == 0) return new int[]{-1, -1};
        int[] res = new int[]{-1, -1};
        int lo = 0, hi = nums.length;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (target > nums[mid]) lo = mid + 1;
            else hi = mid;
        }
        if (lo == nums.length || nums[lo] != target) return res;
        res[0] = lo;
        
        lo = 0; hi = nums.length;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (target >= nums[mid]) lo = mid + 1;
            else hi = mid;
        }
        res[1] = lo - 1;
        return res;
    }
}
```

## Solution 2: 2-pass by 1-method

- $TC:O(logn)$
- $SC:O(1)$
- 还是调用两次二分搜索，不过只需要实现一个二分搜索的方法。
- 先在nums中搜target找到第一个位置，然后在nums中搜target+1找到最后一个位置的下一个位置。

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int first = binarySearch(nums, target);
        if (first == nums.length || nums[first] != target) {
            return new int[]{-1, -1};
        }
        int last = binarySearch(nums, target + 1);
        return new int[]{first, last - 1};
    }
    
    private int binarySearch(int[] nums, int target) {
        int lo = 0, hi = nums.length;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

## Solution 3: Cheating

- $TC:O(logn)$
- $SC:O(1)$
- 因为nums是一个有序的整数序列，我们可以在nums中找(target - 0.5)以及(target + 0.5)对应的出现位置。
- (target - 0.5)对应的是target第一次出现的位置，而(target + 0.5)对应的是target最后一次出现的位置的下一个位置。
- 如果找到的两个位置相同，说明nums中不存在target。

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        double left = target - 0.5, right = target + 0.5;
        int first = binarySearch(nums, left), last = binarySearch(nums, right);
        if (first == last) return new int[]{-1, -1};
        return new int[]{first, last - 1};
    }
    
    private int binarySearch(int[] nums, double target) {
        int lo = 0, hi = nums.length;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```