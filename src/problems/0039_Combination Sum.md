# 39. Combination Sum (2021.02.08)

https://leetcode.com/problems/combination-sum/

## Solution 1: Backtracking

- $TC:O((target/min)*n^(target/min))$
- $SC:O((target/min)*n^(target/min))$
- 本题的时间复杂度和空间复杂度不太好分析，因此不大肯定上述是正确的
- 和Permutation以及Subset的思想类似，都是基于backtrack搜索可能的结果

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> ans = new ArrayList<>();
        // 先排个序，之后可以根据当前candidate是否大于剩下的target来提前结束递归
        Arrays.sort(candidates);
        backtracking(candidates, target, 0, ans, res);
        return res;
    }
    
    private void backtracking(int[] candidates, 
                              int target, 
                              int index, 
                              List<Integer> ans, 
                              List<List<Integer>> res) {
        if (target == 0) res.add(new ArrayList<>(ans));
        
        for (int i = index; i < candidates.length; i++) {
            if (candidates[i] > target) return;
            
            ans.add(candidates[i]);
            // 因为我们可以复用同一个元素，所以还是从i开始递归
            backtracking(candidates, target - candidates[i], i, ans, res);
            ans.remove(ans.size() - 1);
        }
    }
}
```

## Solution 2: DP
https://leetcode.com/problems/combination-sum/discuss/16509/Iterative-Java-DP-solution