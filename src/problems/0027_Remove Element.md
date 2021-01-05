## 27. Remove Element (2020.05.06)

https://leetcode.com/problems/remove-element/

## Solution 1: Two pointers (keep order)

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if (nums == null) return 0;
        
        int i = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] != val) nums[i++] = nums[j];
        }
        
        return i;
    }
}
```

## Solution 2: Two pointers

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if (nums == null) return 0;
        
        int i = 0, j = nums.length - 1;
        while (i <= j) {
            if (nums[i] == val) nums[i] = nums[j--];
            else i++;
        }
        
        return j + 1;
    }
}
```

