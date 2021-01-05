## 136. Single Number (2020.08.03)

https://leetcode.com/problems/single-number/

## Solution 1: Bit manipulation -> Use XOR

- $TC:O(N)$
- $SC:O(1)$
- XOR同时满足交换律和结合律 -> a ^ b = b ^ a; (a ^ b) ^ c = a ^ (b ^ c)
- 因此，array中出现偶次的数字会异或为0，最终剩下只出现一次的数字（或者奇次）

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0;
        for (int i = 0; i < nums.length; i++) res = res ^ nums[i];
        return res;
    }
}
```

## Solution 2: Sorting, then iterate

- $TC:O(NlogN)$
- $SC:O(1)$

```java
class Solution {
    public int singleNumber(int[] nums) {
        Arrays.sort(nums);
        int res = nums[nums.length - 1];
        
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] != nums[i + 1]) res = nums[i];
            else i++;
        }
        
        return res;
    }
}
```

## Solution 3: HashMap

- $TC:O(N)$
- $SC:O(M)$, M is the number in nums without duplication

```java
class Solution {
    public int singleNumber(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int num : nums) {
            if (map.containsKey(num)) map.put(num, map.get(num) + 1);
            else map.put(num, 1);
        }
        
        int res = 0;
        
        for (int key : map.keySet()) {
            if (map.get(key) == 1) res = key;
        }
        
        return res;
    }
}
```

