# 46. Permutations (2021.01.31)

https://leetcode.com/problems/permutations/

## Solution 1: Build from previous results (iterative, backtracking)

- $TC:O(n*n!)$
- $SC:O(n*n!)$，即使忽略res所需要的空间，也需要$O(n*n!)$，因为要保存temp来构造下一个结果。
- 该解法的原理为：n个数的所有排列 = 将第n个数插入到n-1个数的各个排列结果的合适位置。
- 比如，我们要求[1,2,3]的所有排列，那么当我们知道[1,2]的所有排列为[1,2]和[2,1]时，我们可以将3插入来构造新的排列结果。
- 即：[1,2] -> [3,1,2],[1,3,2],[1,2,3], [2,1] -> [3,2,1],[2,3,1],[2,1,3]。
- 由于没有重复的元素，所以可以将新元素插入到0~n-1的所有位置，以此类推构造出数组的全排列。

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) return res;
        
        // 初始状态：res = [[nums[0]]]
        List<Integer> l0 = new ArrayList<>();
        l0.add(nums[0]);
        res.add(l0);
        
        // 从nums[1]开始插入和扩展
        for (int i = 1; i < nums.length; i++) {
            // 用于保存从0到i这一子数组的全排列结果
            List<List<Integer>> temp = new ArrayList<>();
            // 对从0到i-1的每个排列结果都要遍历到
            for (List<Integer> permutation : res) {
                // 对于每一个排列，可插入位置为0~i
                for (int j = 0; j <= i; j++) {
                    // 基于原排列结果进行扩展
                    List<Integer> copy = new ArrayList<>(permutation);
                    copy.add(j, nums[i]);
                    temp.add(copy);
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
- 从第一个元素开始，每次从n个元素中按序抽出尚未被加入的元素构建排列。
- 然后通过回溯对应位置的下一个可能的元素。

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) return res;

        // 用boolean数组来记录某个元素是否已经包含在被构建的排列中
        boolean[] isContained = new boolean[nums.length];
        backtrack(nums, isContained, new ArrayList<>(), res);
        return res;
    }
    
    private void backtrack(int[] nums, boolean[] isContained, List<Integer> temp, List<List<Integer>> res) {
        // 当该轮被构建的排列temp和nums一样长时，说明temp是我们想要的一个排列结果
        if (temp.size() == nums.length) {
            res.add(new ArrayList<>(temp));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            // 如果nums[i]已经被包含在temp中，不需要重复加入
            if (isContained[i]) continue;
            
            isContained[i] = true;
            temp.add(nums[i]);
            backtrack(nums, isContained, temp, res);
            // 递归结束之后需要将状态还原再进入下一次循环
            temp.remove(temp.size() - 1);
            isContained[i] = false;
        }
    }
}
```

## Solution 3: Backtracking + Swap

- $TC:O(n!)$ -> n * (n-1) * (n-2) * ... * 1, 每次递归idx往前推进1
- $SC:O(n*n!)$，如果不考虑返回的res的空间，那么只需要$O(n)$
- 通过不断交换来构造出所有可能的排列。

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
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
        
        for (int i = idx; i < nums.length; i++) {
            swap(nums, idx, i);
            backtrack(nums, res, idx + 1);
            swap(nums, idx, i);
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```