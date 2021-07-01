# 162. Find Peak Element (2021.04.07)

https://leetcode.com/problems/find-peak-element/

## Solution 1: Iteration

- $TC:O(n)$
- $SC:O(1)$
- 最朴素的解法，就是遍历一遍nums，找到peak。
- 因为nums[-1]默认为负无穷，所以从nums[-1]到nums[0]就可以看做一个上升的区间。
- 我们只需要从i=1开始，检查nums[i]是否比nums[i-1]来得小，如果发现符合条件的i，就说明找到了一个下降的区间。所以，我们就找到了一个peak=i。
- 不过也有可能nums是一个单调递增序列，所以循环结束都没返回的话，说明最后一个元素是peak。

```java
class Solution {
    public int findPeakElement(int[] nums) {
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] < nums[i - 1]) return i - 1;
        }
        return nums.length - 1;
    }
}
```

## Solution 2: Binary Search (Iterative)

- $TC:O(logn)$
- $SC:O(1)$
- 一般来说，二分搜索都是用于有序数组的情况，而本题的nums不能确保有序性，而且还可能存在忽上忽下的多个peak，为什么可以使用二分搜索呢？
- 我们可以从多个角度来解释：
- 第一种解释是我们在求出mid后，将nums[mid]和nums[mid+1]进行比较，如果nums[mid]大于nums[mid+1]，说明至少我们可以确定mid+1到mid是上升的。
- 加上nums[-1]为负无穷，那么在0到mid这个区间内，或者存在真正的peak，或者是不断递增在nums[0]获得最大。
- 所以我们可以确定peak必然会出现在左半部分的搜索范围内，就将hi设置为mid进行下一轮搜索。nums[mid]大于nums[mid+1]同理。
- 第二种解释就是从导数的角度切入，因为nums[-1]以及nums[n]均为负无穷，所以导数d(-1) > 0，d(n-1) < 0。
- 这说明在[0, n-1]间必定存在使d转变正负性的拐点peak。所以，如果我们在搜索时发现nums[mid] < nums[mid+1]，则对应的d(mid) > 0。
- 根据d(mid) > 0，我们可以确定[mid+1, n-1]之间必定存在使d转为负数的拐点peak，因此将下一次的搜索范围设置到这一部分。
- 相反地，因为d(-1) > 0且d(mid) > 0，我们是无法断定[0, mid]之间是存在peak的。
- 还有一种解释是从invariant的角度切入的，在最初状态下，lo = 0，hi = n-1，对应有nums[-1] < nums[lo]和nums[n] < nums[hi]。
- 在随后的搜索中，我们始终保持了nums[lo-1] < nums[lo]且nums[hi+1] < nums[hi]这一不变性，直到lo和hi收敛至相同位置。
- 那么最后必定有nums[lo-1] < nums[lo]且nums[hi+1] < nums[hi]且nums[lo] = nums[hi]，lo就是我们要找的peak的index。

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int lo = 0, hi = nums.length - 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] > nums[mid + 1]) hi = mid;
            else lo = mid + 1;
        }
        return lo;
    }
}
```

## Solution 3: Binary Search (Recursive)

- $TC:O(logn)$
- $SC:O(logn)$
- Solution2的递归实现。

```java
class Solution {
    public int findPeakElement(int[] nums) {
        return binarySearch(nums, 0, nums.length - 1);
    }
    
    private int binarySearch(int[] nums, int lo, int hi) {
        if (lo >= hi) return lo;
        
        int mid = (hi - lo) / 2 + lo;
        if (nums[mid] < nums[mid + 1]) return binarySearch(nums, mid + 1, hi);
        return binarySearch(nums, lo, mid);
    }
}
```