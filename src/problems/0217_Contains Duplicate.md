## 217. Contains Duplicate (2020.08.13)

https://leetcode.com/problems/contains-duplicate/

## Solution 1: Sorting -- 时间换空间

- $TC:O(NlogN)$
- $SC:O(1)$

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        if (nums == null) return false;
        
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] == nums[i + 1]) return true;
        }
        
        return false;
    }
}
```

## Solution 2: HashSet -- 空间换时间

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        if (nums == null) return false;
        
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < nums.length; i++) {
            if (set.contains(nums[i])) return true;
            set.add(nums[i]);
        }
        
        return false;
    }
}
```

