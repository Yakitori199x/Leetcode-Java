## 35. Search Insert Position (2020.05.11)

https://leetcode.com/problems/search-insert-position/

## Solution 1: Binary search ver1

- $TC:O(logn)$
- $SC:O(1)$
- As j strats with nums.length - 1, you should check nums[j] even if i = j, so the condition of while loop should be i <= j, and assign (mid-1) to j when nums[mid] > target

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int i = 0, j = nums.length - 1;
        
        while (i <= j) {
            int mid = i + (j - i) / 2;
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) i = mid + 1;
            else j = mid - 1;
        }
        
        return i;
    }
}
```

## Solution 2: Binary Search ver2

- $TC:O(logn)$
- $SC:O(1)$
- As j strats with nums.length, you don't need to check nums[j] even if i = j, so the condition of while loop should be i < j, and assign (mid) to j when nums[mid] > target

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int i = 0, j = nums.length;
        
        while (i < j) {
            int mid = i + (j - i) / 2;
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) i = mid + 1;
            else j = mid;
        }
        
        return i;
    }
}
```

