# 33. Search in Rotated Sorted Array (2021.04.05)

https://leetcode.com/problems/search-in-rotated-sorted-array/

## Related Questions:
- 153. Find Minimum in Rotated Sorted Array

## Solution 1: 2-pass, mappping index

- $TC:O(logn)$
- $SC:O(1)$
- 进行两轮二分搜索，通过第一轮二分搜索先找到nums中min的位置。
- 知道min的位置之后，我们就可以获得rotated之前的nums和当前nums各元素的index间的映射，比如0->minPos%n->(1+minPos)%n，...，k->(k+minPos)%n。
- 有个这么个index之间的mapping，我们就可以对rotated前的nums（单调递增数组）进行二分搜索找到target。
- 因为每次搜索只需要关注mid对应的值和target的关系，我们不需要复原rotated前的nums，而是获得一个mid后根据index的mapping获得其在当前nums对应位置的值。

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] > nums[hi]) lo = mid + 1;
            else hi = mid;
        }
        
        int minPos = lo;
        lo = 0; hi = n;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo, realIdx = (mid + minPos) % n;
            if (nums[realIdx] == target) return realIdx;
            else if (nums[realIdx] < target) lo = mid + 1;
            else hi = mid;
        }
        return -1;
    }
}
```

## Solution 2: 2-pass, choose left or right part

- $TC:O(logn)$
- $SC:O(1)$
- 和Solution1一样，先进行一遍二分搜索找到min的位置，min的位置将nums分割成了左右两部分单调递增数组。
- 之后我们可以根据target的大小来判断其位于左侧还是右侧，如果target >= nums[min]且target <= nums[hi]，说明其位于右侧。
- 否则，说明其位于左侧，对应修改lo和hi的值然后进行二分搜索即可。

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] > nums[hi]) lo = mid + 1;
            else hi = mid;
        }
        
        int delimiter = lo;
        lo = 0; hi = n - 1;
        if (target >= nums[delimiter] && target <= nums[hi]) lo = delimiter;
        else hi = delimiter;

        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) lo = mid + 1;
            else hi = mid - 1;
        }
        return -1;
    }
}
```

## Solution 3: 1-pass

- $TC:O(logn)$
- $SC:O(1)$
- 1-pass的解法直接根据rotated sorted array的性质去找target，不需要提前过一遍二分搜索找到min的位置。

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if ((nums[mid] - nums[0]) * (target - nums[0]) > 0) {
                // 判断mid和target是否在同一侧
                // 这里需要要以nums[0]作为比较标准
                // 如果用的是nums[n-1]，会在导致nums=[1,3]，target=1的情况下将mid和target判断为在不同侧
                // 由于mid和target在同一侧，就可以直接用nums[mid]与target比较确定下一次的搜索范围
                if (nums[mid] < target) lo = mid + 1;
                else hi = mid;
            } else if (target < nums[0]) {
                // 否则，mid和target在不同侧，此为mid在左半侧，target在右半侧的情况
                lo = mid + 1;
            } else {
                // 此为mid在右半侧，target在左半侧的情况
                hi = mid;
            }
            
            if (nums[mid] == target) return mid;
        }
        return -1;
    }
}
```

## Solution 4: 1-pass

- $TC:O(logn)$
- $SC:O(1)$
- 每次搜索过程中，先判断mid处于左半部分还是右半部分。
- 确定mid位于左半部分还是右半部分后，就继续检查target是位于mid的左侧还是右侧，并根据这个结果更新下次搜索的范围。

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length, lo = 0, hi = n - 1;
        while (lo <= hi) {
            int mid = (hi - lo) /2 + lo;
            if (nums[mid] == target) return mid;
            if (nums[mid] >= nums[lo]) {
                if (nums[lo] <= target && target < nums[mid]) {
                    hi = mid - 1;
                } else {
                    lo = mid + 1;
                }
            } else {
                if (nums[mid] < target && target <= nums[hi]) {
                    lo = mid + 1;
                } else {
                    hi = mid - 1;
                }
            }
        }
        return -1;
    }
}
```