## 80. Remove Duplicates from Sorted Array II (2020.10.03)

https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/

## Solution 1: Two pointers

- $TC:O(N)$
- $SC:O(1)$
- 一个pointer用来遍历数组，另一个pointer用来记录当前可以被替换的位置

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int i = 0, j = 0;
        
        while (j < nums.length) {
            // can not be nums[j] > nums[j - 2]
            // e.g. nums: [1,1,1,2,2,3]
            if (i < 2 || nums[j] > nums[i - 2]) {
                nums[i++] = nums[j];
            }
            j++;
        }
        
        return i;
    }
}
```

## Solution 2: Two pointers

- $TC:O(N)$
- $SC:O(1)$
- 和Solution 1的思想类似，不过维护了一个cnt来计算数字的重复次数

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int i = 1, j = 1, cnt = 1;
        
        while (j < nums.length) {
            if (nums[j] > nums[j - 1]) {
                nums[i++] = nums[j];
                cnt = 1;
            } else {
                if (cnt < 2) {
                    nums[i++] = nums[j];
                    cnt++;
                }
            }
            
            j++;
        }
        
        return i;
    }
}
```

