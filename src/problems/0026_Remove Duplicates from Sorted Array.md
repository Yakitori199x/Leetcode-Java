## 26. Remove Duplicates from Sorted Array (2020.05.05)

https://leetcode.com/problems/remove-duplicates-from-sorted-array/

## Solution 1: Two pointers

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;
        for (int j = 1; j < nums.length; j++) {
            if (nums[j] != nums[i]) {
                nums[++i] = nums[j];
            }
        }
        
        return i + 1;
    }
}
```

