# 698. Partition to K Equal Sum Subsets (2021.02.23)

https://leetcode.com/problems/partition-to-k-equal-sum-subsets/

## Solution 1: DFS, backtracking ver1

- $TC:O(k^n)$
- $SC:O(n)$
- 和LC473的Solution1是同一种解法，需要做一些优化才可以达到比较快的效率。

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % k != 0) return false;
        
        Arrays.sort(nums);
        reverse(nums);
        if (nums[0] > sum / k) return false;
        return dfs(nums, new int[k], 0, sum / k);
    }
    
    private boolean dfs(int[] nums, int[] subsets, int index, int target) {
        if (index == nums.length) {
            for (int i = 0; i < subsets.length; i++) {
                if (subsets[i] != target) return false;
            }
            return true;
        }
        
        for (int i = 0; i < subsets.length; i++) {
            if (subsets[i] + nums[index] > target) continue;
            
            int j = i;
            while (--j >= 0) {
                if (subsets[i] == subsets[j]) break;
            }
            if (j >= 0) continue;
            
            subsets[i] += nums[index];
            if (dfs(nums, subsets, index + 1, target)) return true;
            subsets[i] -= nums[index];
        }
        
        return false;
    }
    
    private void reverse(int[] nums) {
        int i = 0, j = nums.length - 1;
        while (i < j) {
            int temp = nums[i];
            nums[i++] = nums[j];
            nums[j--] = temp;
        }
    }
}
```

## Solution 2: DFS, backtracking ver2

- $TC:O(k*2^n)$
- $SC:O(n)$
- 该解法实际上就是不断搜索nums的子集，直到找到一个和为target的子集。
- 找到一个有效的子集后，将待寻找的subsets个数递减（k-1），并重置当前的子集和（curSum）继续搜索下去，直至找到结果。
- 所以，对于每一层的k来说，dfs中的for循环就是在遍历nums的所有可能的子集，其数量为2^n个。
- 这一过程在每次形成新的有效子集时都要进行一次，直到k=1。

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % k != 0) return false;
        
        Arrays.sort(nums);
        reverse(nums);
        if (nums[0] > sum / k) return false;
        
        boolean[] visited = new boolean[nums.length];
        return dfs(nums, visited, 0, 0, k, sum / k);
    }
    
    private boolean dfs(int[] nums, boolean[] visited, int curSum, int index, int k, int target) {
        // 当只剩下一个subset还没确定时
        // 就可以确定nums能够被均等地分到k个subsets
        if (k == 1) return true;
        
        // 当前的nums的和等于target，形成一个有效的subset
        if (curSum == target) {
            // 把待定的subsets数量减去1
            // 同时将curSum和index重置，进入下一次递归
            return dfs(nums, visited, 0, 0, k - 1, target);
        }
        
        for (int i = index; i < nums.length; i++) {
            // 因为在形成一个有效subset后会重置index到0再递归下去
            // visited主要是用来防止重复加入已经被用于构成之前的subsets的nums[i]
            // 但是对于k相等的一系列递归来说没有效果，因为index会控制下次递归的起始点
            if (!visited[i] && curSum + nums[i] <= target) {
                visited[i] = true;
                if (dfs(nums, visited, curSum + nums[i], index + 1, k, target)) return true;
                // 切记将visited标记为false，否则一旦之后形成有效的subset进入k-1的递归时
                // for循环内的逻辑会把可以加入的nums[i]也误判为已经被visited
                visited[i] = false;
            }
        }
        
        return false;
    }
    
    private void reverse(int[] nums) {
        int i = 0, j = nums.length - 1;
        while (i < j) {
            int temp = nums[i];
            nums[i++] = nums[j];
            nums[j--] = temp;
        }
    }
}
```

## Solution 3: DP, bitmask
https://leetcode.com/problems/partition-to-k-equal-sum-subsets/discuss/335668/DP-with-Bit-Masking-Solution-%3A-Best-for-Interviews