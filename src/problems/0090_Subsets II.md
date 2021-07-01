# 90. Subsets II (2021.02.03)

https://leetcode.com/problems/subsets-ii/

## Related Questions
- 46. Permutations
- 78. Subsets

## Solution 1: Cascading I

- $TC:O(n*2^n)$
- $SC:O(n*2^n)$，如果不考虑返回的res的空间，那么只需要$O(n)$
- 为了防止生成重复的subset，发现duplicate的情况下，只需要将当前元素加入上次循环新生成的subsets。
- 比如：[1,2,3,3] -> 加入第一个3后变为[[], [1], [1,2], [2], [3], [1,3], [1,2,3], [2,3]]
- 遇到重复的3的时候，只需要对上次新加入的[[3], [1,3], [1,2,3], [2,3]]进行扩展即可。

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> l0 = new ArrayList<>();
        res.add(l0);
        // 需要先排好序
        Arrays.sort(nums);
        
        int size = res.size();
        for (int i = 0; i < nums.length; i++) {
            // 如果发现duplicate，则把start设置为上一次迭代时候res的size
            // 即从上一次迭代时新加入的第一个subset开始遍历
            int start = ((i == 0) || (nums[i] != nums[i - 1])) ? 0 : size; 
            size = res.size();
            for (int j = start; j < size; j++) {
                List<Integer> temp = new ArrayList<>(res.get(j));
                temp.add(nums[i]);
                res.add(temp);
            }
        }
        
        return res;
    }
}
```

## Solution 2: Cascading II

- $TC:O(n*2^n)$
- $SC:O(n*2^n)$，如果不考虑返回的res的空间，那么只需要$O(n)$
- 与Solution 1不同的是，这个方法是一次性将连续的值的个数计算出来（若只有一个，则count=1）然后加入
- 比如[1,2,2] -> [[], [1]] -> 发现存在两个2 -> [[], [1], [2], [2,2]] -> [[], [1], [2], [2,2], [1,2], [1,2,2]]

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> l0 = new ArrayList<>();
        res.add(l0);
        Arrays.sort(nums);
        
        int i = 0;
        while (i < nums.length) {
            // 计算nums[i]出现的次数
            int count = 0;
            while (count + i < nums.length && nums[i] == nums[count + i]) count++;
            
            int size = res.size();
            for (int j = 0; j < size; j++) {
                List<Integer> temp = new ArrayList<>(res.get(j));
                for (int k = 0; k < count; k++) {
                    // 向temp中添加count次nums[i]，注意每加入一次就是一个新的subset
                    temp.add(nums[i]);
                    res.add(new ArrayList<>(temp));
                }
            }
            
            i += count;
        }
        
        return res;
    }
}
```

## Solution 3: Backtracking

- $TC:O(n*2^n)$
- $SC:O(n*2^n)$，如果不考虑返回的res的空间，那么只需要$O(n)$

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> l0 = new ArrayList<>();
        Arrays.sort(nums);
        backtracking(nums, 0, l0, res);
        return res;
    }
    
    private void backtracking(int[] nums, 
                              int index, 
                              List<Integer> l0, 
                              List<List<Integer>> res) {
        res.add(new ArrayList<>(l0));
        for (int i = index; i < nums.length; i++) {
            if (i != index && nums[i] == nums[i - 1]) continue;
            l0.add(nums[i]);
            backtracking(nums, i + 1, l0, res);
            l0.remove(l0.size() - 1);
        }
    }
}
```