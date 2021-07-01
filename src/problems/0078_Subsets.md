# 78. Subsets (2021.02.03)

https://leetcode.com/problems/subsets/

## Related Questions
- 46. Permutations

## Solution 1: Cascading

- $TC:O(n*2^n)$ -> 每次加入获得新的subset之前都需要deep copy之前的subset
- $SC:O(n*2^n)$，如果不考虑返回的res的空间，那么只需要$O(n)$
- 从空集开始，每次往之前的subsets里加入当前元素nums[i]生成新的subsets

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> l0 = new ArrayList<>();
        
        res.add(l0);
        for (int i = 0; i < nums.length; i++) {
            int size = res.size();
            for (int j = 0; j < size; j++) {
                List<Integer> temp = new ArrayList<>(res.get(j));
                temp.add(nums[i]);
                res.add(temp);
            }
        }
        
        return res;
    }
}
```

## Solution 2: Backtracking

- $TC:O(n*2^n)$ -> 每次加入新的subset都需要deep copy
- $SC:O(n*2^n)$，如果不考虑返回的res的空间，那么只需要$O(n)$

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        backtracking(nums, new ArrayList<>(), res, 0);
        return res;
    }
    
    private void backtracking(int[] nums, 
                              List<Integer> temp, 
                              List<List<Integer>> res, 
                              int index) {
        // 当第一次进入递归（即i=index）或是index=nums.length时需要加入subset
        // 所以可以直接将这个逻辑放到循环外面做
        res.add(new ArrayList<>(temp));
        for (int i = index; i < nums.length; i++) {
            temp.add(nums[i]);
            backtracking(nums, temp, res, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

## Solution 3: Bit Manipulation

- $TC:O(n*2^n)$
- $SC:O(n*2^n)$，如果不考虑返回的res的空间，那么只需要$O(n)$。而且由于subsets数量已知，可以初始化一个固定长度的res，然后res.get(i).add(nums[j])来添加元素，可以把空间复杂度降低到$O(1)$
- 由于subset的构成就是list中的每个元素有或是没有，因此可以用一个n位的binary sequence来表示对应位置元素是否包含在subset中。

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        // m表示了subsets的总数，同时也是bitMask(即i)的上界
        int m = (1 << n);
        
        for (int i = 0; i < m; i++) {
            List<Integer> l = new ArrayList<>();
            for (int j = 0; j < n; j++) {
                // 查看i的每一位是否为1，如果为1，说明这位置对应的nums[j]应该被加入subset
                if ((i >> j & 1) == 1) l.add(nums[j]);
            }
            res.add(l);
        }
        
        return res;
    }
}
```