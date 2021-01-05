## 219. Contains Duplicate II (2020.08.13)

https://leetcode.com/problems/contains-duplicate-ii/

## Solution 1: HashMap

- $TC:O(N)$
- $SC:O(N)$
- 遍历到新的num时，检查num的当前index和之前记录在hashmap里的index（若存在）之差是否不大于k

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        if (nums == null) return false;
        
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i])) {
                if (i - map.get(nums[i]) <= k) return true;
            }
            
            map.put(nums[i], i);
        }
        
        return false;
    }
}
```

## Solution 2: HashSet (Sliding window)

- $TC:O(N)$
- $SC:O(N)$
- 维护一个长度为k+1的滑动窗口，即每次只关注k+1个数的情况。
- 若i大于k，则先移除窗口最左端的nums[i - k - 1]，然后向set中加入最新的nums[i] -> 相当于向右移动窗口。如果检测到重复，则说明存在duplicate numbers并且difference不大于k。

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        if (nums == null) return false;
        
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < nums.length; i++) {
            if (i > k) set.remove(nums[i - k - 1]);
            if (!set.add(nums[i])) return true;
        }
        
        return false;
    }
}
```

