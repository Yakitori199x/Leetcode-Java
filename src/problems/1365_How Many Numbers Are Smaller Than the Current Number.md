# 1365. How Many Numbers Are Smaller Than the Current Number (2021.05.18)

https://leetcode.com/problems/how-many-numbers-are-smaller-than-the-current-number/

## Solution 1: Counting

- $TC:O(n)$
- $SC:O(1)$
- 由于nums[i]的范围在0到100之间，我们可以直接使用一个数组来记录每个值的出现次数。
- 然后从i=1开始不断将count[i]和count[i-1]累加，这样一来count[i]就对应着nums中比i+1小的所有数的个数。

```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {
        int[] count = new int[101], res = new int[nums.length];
        for (int num : nums) count[num]++;
        for (int i = 1; i < 101; i++) count[i] += count[i - 1];
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 0) res[i] = 0;
            else res[i] = count[nums[i] - 1];
        }
        return res;
    }
}
```

## Solution 2: HashMap

- $TC:O(nlogn)$
- $SC:O(n)$
- 虽然本题基于nums[i]的范围在0到100之间这个假设，但可能在其他情况下该假设就不成立了，也就是说nums[i]的取值范围可能很大，那么Solution1就不大合适了。
- 该解法是一个相对通用的解法。

```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {
        int n = nums.length;
        int[] copy = Arrays.copyOf(nums, n), res = new int[n];
        Map<Integer, Integer> map = new HashMap<>();
        
        Arrays.sort(copy);
        for (int i = 0; i < n; i++) map.putIfAbsent(copy[i], i);
        for (int i = 0; i < n; i++) res[i] = map.get(nums[i]);
        return res;
    }
}
```