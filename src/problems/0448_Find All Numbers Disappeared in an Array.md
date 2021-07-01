## 448. Find All Numbers Disappeared in an Array (2021.01.11)

https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/

## Solution 1: Negate values in array

- $TC:O(n)$
- $SC:O(1)$
- 根据题设可知，1 ≤ nums[i] ≤ n。因此可以从头遍历nums数组，将nums[i]-1作为索引。然后把对应位置的nums[idx]的值设置为负数，表示idx+1这个值已经出现在数组中。
- 之后再次遍历nums，值为正数的对应位置的idx+1即为消失的数。

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        if (nums == null) return new ArrayList<>();
        
        for (int i = 0; i < nums.length; i++) {
            // 取绝对值，因为之前的操作可能将该位置的值设置为负
            int idx = Math.abs(nums[i]) - 1;
            // 如果之前已经设置为负，则不需要再次翻转符号
            nums[idx] = (nums[idx] > 0) ? -nums[idx] : nums[idx];
        }
        
        List<Integer> res = new ArrayList<>();
        
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > 0) res.add(i + 1);
        }
        
        return res;
    }
}
```

## Solution 2: Swap

- TC:O(n)
- SC:O(1)
- 将i处的值nums[i]与nums[i]-1处的值nums[nums[i]-1]交换，把nums[i]放到正确的位置。
- 比如，若nums[0]=6,此时我们的目的就是将6放置到nums[5]的位置，使其满足nums[i]=i+1。
- 待所有能够交换的情况都执行过后，遍历nums，找出所有不满足nums[i]=i+1的情况，则为消失的数。

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        if (nums == null) return new ArrayList<>();
        
        int i = 0;
        while (i < nums.length) {
            int val = nums[i];
            // 当nums[i] = i + 1或是nums[nums[i] - 1] = nums[i]，
            // 即i处的值已经为i+1，或者nums[i] - 1处的值已经为nums[i]时(因为有重复的值，所以之前可能已经被放到相应的位置)，不需要交换
            // 因此，每次循环，要么递增i，要么交换使一个值放置到我们希望的位置
            if (nums[val - 1] != val) {
                nums[i] = nums[val - 1];
                nums[val - 1] = val;
            } else {
                i++;
            }
        }
        
        List<Integer> res = new ArrayList<>();
        for (i = 0; i < nums.length; i++) {
            if (nums[i] != i + 1) res.add(i + 1);
        }
        
        return res;
    }
}
```