## 283. Move Zeroes (2020.07.13)

https://leetcode.com/problems/move-zeroes/

## Solution 1: Two pointers

- $TC:O(N)$
- $SC:O(1)$
- One pointer i for iteration, the other idx for recording the insert position. When nums[i] is not equal to 0, swap it with nums[idx].

```java
class Solution {
    public void moveZeroes(int[] nums) {
        if (nums == null || nums.length == 0) return;
        
        int idx = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                int tmp = nums[idx];
                nums[idx++] = nums[i];
                nums[i] = tmp;
            }
        }
    }
}
```

