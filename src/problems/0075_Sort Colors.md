## 75. Sort Colors (2020.07.27)

https://leetcode.com/problems/sort-colors/

## Solution 1: 2-pass solution

- $TC:O(N)$
- $SC:O(1)$
- Record the number of 0, 1 and 2 in nums
- Re-assign the value to nums

```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) return;
        
        int zero = 0, one = 0, two = 0;
        for (int num : nums) {
            if (num == 0) zero++;
            else if (num == 1) one++;
            else two++;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (zero > 0) {
                nums[i] = 0;
                zero--;
            } else if (one > 0) {
                nums[i] = 1;
                one--;
            } else {
                nums[i] = 2;
                two--;
            }
        }
    }
}
```

## Solution 2: 1-pass (Dutch Partitioning Problem)

- $TC:O(N)$
- $SC:O(1)$
- Maintain three pointers, i: the end of 0, j: the end of 1, k: the begin of 2
- Don't forget to check the case when j = k

```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) return;
        
        int i = 0, j = 0, k = nums.length - 1;
        while (j <= k) {
            if (nums[j] == 0) {
                nums[j++] = nums[i];
                nums[i++] = 0;
            } else if (nums[j] == 2) {
                nums[j] = nums[k];
                nums[k--] = 2;
            } else {
                j++;
            }
        }
    }
}
```

## Solution 3: 1-pass, clever workaround!

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) return;
        
        int i = -1, j = -1, k = -1;
        for (int idx = 0; idx < nums.length; idx++) {
            if (nums[idx] == 0) {
                nums[++k] = 2;
                nums[++j] = 1;
                nums[++i] = 0;
            } else if (nums[idx] == 1) {
                nums[++k] = 2;
                nums[++j] = 1;
            } else {
                nums[++k] = 2;
            }
        }
    }
}
```

