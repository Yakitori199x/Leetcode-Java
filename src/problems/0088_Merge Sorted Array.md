## 88. Merge Sorted Array (2020.05.28)

https://leetcode.com/problems/merge-sorted-array/

## Solution 1: Compare from end to head

- $TC:O(m+n)$
- $SC:O(1)$

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int pt = m + n - 1;
        int i = m - 1, j = n - 1;
        
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) nums1[pt--] = nums1[i--];
            else nums1[pt--] = nums2[j--];
        }
        
        // No need to check for i when the while loop ends with j = 0
        // Because top i numbers are already sorted and in right places
        while (j >= 0) nums1[pt--] = nums2[j--];
    }
}
```

