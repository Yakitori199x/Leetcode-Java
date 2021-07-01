# 47. Permutations II (2021.01.31)

https://leetcode.com/problems/permutations-ii/

## Related Questions:
- 46. Permutations

## Solution 1: Build from previous results (iterative, backtracking)

- $TC:O(n*n!)$
- $SC:O(n*n!)$，即使忽略res所需要的空间，也需要$O(n*n!)$，因为要保存temp来构造下一个结果。
- 解法和Leetcode.46基本相同，只不过这一题需要考虑duplicates

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) return res;
        
        List<Integer> l0 = new ArrayList<>();
        l0.add(nums[0]);
        res.add(l0);
        
        for (int i = 1; i < nums.length; i++) {
            List<List<Integer>> temp = new ArrayList<>();
            for (List<Integer> permutation : res) {
                for (int j = 0; j <= i; j++) {
                    List<Integer> copy = new ArrayList<>(permutation);
                    copy.add(j, nums[i]);
                    temp.add(copy);
                    // 不要往duplicate后插入元素，比如将3插入[1,2,3]和[1,3,2]
                    // 从[1,2,3]可以获得[1,3,2,3]，如果在[1,3,2]的3之后还继续插入，则会出现重复的排列
                    if (j < i && nums[i] == permutation.get(j)) break;
                }
            }
            
            res = temp;
        }
        
        return res;
    }
}
```

## Solution 2: Backtracking (DFS)

- $TC:O(n*n!)$
- $SC:O(n*n!)$，如果不考虑返回的res的空间，那么只需要$O(n)$

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) return res;

        boolean[] isContained = new boolean[nums.length];
        // 对nums进行排序，以便后续消除duplicate的影响
        Arrays.sort(nums);
        backtrack(nums, isContained, new ArrayList<>(), res);
        return res;
    }
    
    private void backtrack(int[] nums, boolean[] isContained, List<Integer> temp, List<List<Integer>> res) {
        if (temp.size() == nums.length) {
            res.add(new ArrayList<>(temp));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            // 当新的待加入的nums[i]和nums[i-1]相等且nums[i-1]没有被包含在temp中时，直接跳过
            // 可以避免比如[1,2,2,3,...]以nums[1]作为temp第二个元素排列一遍后，再以nums[2]作为第二个元素排列一遍
            // 但是当isContained[i-1]为true时，说明nums[1]已经在temp中，这时候是可以将nums[2]加入为下一个元素的
            if (i > 0 && nums[i] == nums[i - 1] && !isContained[i-1]) continue;
            if (isContained[i]) continue;
            
            isContained[i] = true;
            temp.add(nums[i]);
            backtrack(nums, isContained, temp, res);
            temp.remove(temp.size() - 1);
            isContained[i] = false;
        }
    }
}
```

## Solution 3: Backtracking + Swap

- $TC:O(n!)$ -> n * (n-1) * (n-2) * ... * 1, 每次递归idx往前推进1
- $SC:O(n*n!)$，如果不考虑返回的res的空间，那么只需要$O(n)$

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) return res;
        backtrack(nums, res, 0);
        return res;
    }
    
    private void backtrack(int[] nums, List<List<Integer>> res, int idx) {        
        if (idx == nums.length) {
            List<Integer> permutation = new ArrayList<>();
            for (int num : nums) permutation.add(num);
            res.add(permutation);
            return;
        }
        
        // 为每一层递归维护一个set，如果已经和num[idx]交换过，则不能再次交换
        // 比如: [1,1,2,2,3]
        // 1和第一个2交换后，递归地去求以2为首的所有排列，那么就没必要再和第二个2交换，进行重复求解
        // 同时也说明这里不能靠排序然后略过相邻相等元素，而是不进行排序，靠set来记忆交换过的元素
        Set<Integer> appeared = new HashSet<>();
        for (int i = idx; i < nums.length; i++) {
            if (!appeared.contains(nums[i])) {
                appeared.add(nums[i]);
                swap(nums, idx, i);
                backtrack(nums, res, idx + 1);
                swap(nums, idx, i);
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```