# 473. Matchsticks to Square (2021.02.20)

https://leetcode.com/problems/matchsticks-to-square/

## Solution 1: DFS, Backtracking

- $TC:O(4^N)$
- $SC:O(N)$
- 本题实际上就是一个把nums划分到四个子集中，每个子集的sum相等且等于某个target。
- 每根火柴都有四个可能的选项，即参与构成正方形四条边中的任意一条。
- 该解法的基本思想就是把火柴一根一根地放到相应的边edges[i]中，直到用尽所有火柴。
- 然后检查edges是否满足构成正方形的条件，是则说明当前的火柴组合可以构成正方形。
- 否则回溯到上一层递归调用，将火柴放到下一个可能的edges中。

```java
class Solution {
    public boolean makesquare(int[] nums) {
        // 一系列early stopping的检查
        // 给出的火柴数少于4，直接返回false
        if (nums == null || nums.length < 4) return false;
        
        // 火柴的长度之和无法被4整除，返回false
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % 4 != 0) return false;
        
        // 为了使用Collections.reverseOrder()，必须封装在wrapper里
        Integer[] matches = new Integer[nums.length];
        for (int i = 0; i < nums.length; i++) matches[i] = nums[i];
        
        // 逆向排序，可以优化之后的dfs
        // 同时，检查首个火柴的长度，如果大于target，则返回false
        Arrays.sort(matches, Collections.reverseOrder());
        if (matches[0] > sum / 4) return false;
        
        return dfs(matches, new int[4], 0, sum / 4);
    }
    
    private boolean dfs(Integer[] matches, int[] edges, int index, int target) {
        if (index == matches.length) {
            // 检查三个火柴即可
            if (edges[0] == target && edges[1] == target && edges[2] == target) {
                return true;
            } else {
                return false;
            }
        }
        
        for (int i = 0; i < 4; i++) {
            // 如果当前的edges[i]加上新的火柴已经超出了target，则说明该火柴不适合放在这个边
            // 由于之前的逆向排序，可以避免正向排序时从小火柴开始遍历
            // 会造成很多小火柴堆积在一个edge里，到后面遇上一个大火柴才判断false的情况
            if (edges[i] + matches[index] > target) continue;
            
            // 如果当前的edge和之前的某个edge相等，那继续遍历下去也是把同样的情况重复一次而已
            // 只不过这次可能将火柴放在3，之前将火柴放在1
            int j = i;
            while (--j >= 0) {
                if (edges[i] == edges[j]) break;
            }
            if (j != -1) continue;

            edges[i] += matches[index];
            if (dfs(matches, edges, index + 1, target)) return true;
            edges[i] -= matches[index];
        }
        
        return false;
    }
}
```

## Solution 2: DP, Bit-mask

- $TC:O(N*2^N)$
- $SC:O(2^N)$
- 很有意思的解法，虽然效率比起DFS稍微差了些。

```java
class Solution {
    public boolean makesquare(int[] nums) {
        if (nums == null || nums.length < 4) return false;
        
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % 4 != 0) return false;
        
        Arrays.sort(nums);
        int target = sum / 4, n = nums.length;
        if (nums[n - 1] > target) return false;
        
        // usedMasks用于记录所有对应火柴之和等于target的mask
        List<Integer> usedMasks = new ArrayList<>();
        // validHalfs用于记录对应火柴能形成两个有效的subsets的mask
        boolean[] validHalfs = new boolean[1 << n];
        // 包含了所有火柴的mask，比如(1 << 5) - 1 = 31 = (11111)
        int allMask = (1 << n) - 1;
        
        // 遍历所有的mask，或者说所有的火柴的subsets
        for (int mask = 0; mask <= allMask; mask++) {
            // 求得当前mask下的火柴之和
            int maskSum = 0;
            for (int i = 0; i < n; i++) {
                if ((mask >> i & 1) == 1) maskSum += nums[i];
            }
            
            // 当前的火柴之和等于target，说明该mask形成一个有效的subset
            if (maskSum == target) {
                // 检查所有之前的有效mask
                for (int usedMask : usedMasks) {
                    // 如果当前mask和之前的usedMask没有重合的bit，说明形成了一个validHalf，即两个有效的subsets
                    if ((usedMask & mask) == 0) {
                        // 获取对应validHalf的mask并设置为true
                        int validHalfMask = usedMask | mask;
                        validHalfs[validHalfMask] = true;
                        // 如果另一半也是有效的，说明存在将火柴划分到四个有效的subsets的方法
                        if (validHalfs[allMask ^ validHalfMask]) {
                            return true;
                        }
                    } 
                }
                
                // 将当前的有效mask记录起来
                usedMasks.add(mask);
            }
        }
        
        return false;
    }
}
```