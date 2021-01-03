## 1. Two Sum (2020.03.08)

https://leetcode.com/problems/two-sum/

## Solution 1: Brute force

- $TC:O(N^2)$
- $SC:O(1)$

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[] {i, j};
                }
            }
        }
        
        return null;
    }
}
```

## Solution 2: Two-pass hash table

- $TC: O(N)$ `(As insert and search operations are amortized to O(1) in hash table)`
- $SC:O(N)$
- Cannot deal with the case when there are duplicate elements in array `(e.g. target = 6, nums = [1, 2, 3, 3, ...])`

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement) && map.get(complement) != i) {
                return new int[] {i, map.get(complement)};
            }
        }
        
        return null;
    }
}
```


## Solution 3: One-pass hash table

- $TC:O(N)$
- $SC:O(N)$
- Solve the problem exists in Two-pass hash table

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] {map.get(complement), i};
            }
            
            map.put(nums[i], i);
        }
        
        return null;
    }
}
```
