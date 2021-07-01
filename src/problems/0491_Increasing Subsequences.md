# 491. Increasing Subsequences (2021.02.14)

https://leetcode.com/problems/increasing-subsequences/

## Solution 1: Backtracking (DFS)

- $TC:O(n*2^n)$ -> 每次加入新的subsequence都需要deep copy
- $SC:O(n*2^n)$
- 在worst case的情况下，递增的subsequences基本上等同于list的subsets（除了len至少需要为2这一约束）。
- 本题使用DFS不断构造并探索所有的candidates，并将满足条件的candidates加入最终答案。
- 用DP应该也是可解的，即dp[i]表示到nums[i]为止的list的情况下，所有满足条件的subsequence（可能会带有len为1的seq，最终需要移除）。
- 但在效率上没有什么明显的优势。

```java
class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> seq = new ArrayList<>();
        backtracking(nums, 0, seq, res);
        return res;
    }
    
    private void backtracking(int[] nums, int index, List<Integer> seq, List<List<Integer>> res) {
        // 当subsequence长度至少为2时才能加入res
        if (seq.size() > 1) res.add(new ArrayList<>(seq));
        
        // 用一个set来确保duplicate不会被重复加入
        Set<Integer> prev = new HashSet<>();
        for (int i = index; i < nums.length; i++) {
            if (prev.contains(nums[i])) continue;
            
            // subsequence为空或是nums[i]比seq的最后一个元素大时
            // 将nums[i]加入seq形成新的subsequence
            if (seq.size() == 0 || nums[i] >= seq.get(seq.size() - 1)) {
                seq.add(nums[i]);
                prev.add(nums[i]);
                backtracking(nums, i + 1, seq, res);
                seq.remove(seq.size() - 1);
            }
        }
    }
}
```