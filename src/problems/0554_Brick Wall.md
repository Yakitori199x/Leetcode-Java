# 554. Brick Wall (2021.04.19)

https://leetcode.com/problems/brick-wall/

## Solution 1: Map

- $TC:O(m*n)$，m为wall的rows数，n为rows对应的砖块数的最大者
- $SC:O(n)$
- 根据题设可以看出，要使切过的砖块数最少，可能的选择必定是沿着砖块的edge进行切分（除了wall左右两侧的edge）。
- 因此，我们可以维护一个map，map存储的是砖块的edge（即切分位置）到该edge对应的砖块数的映射。
- 这样一来，我们只需要遍历wall的每一行，获取每一个砖块的edge（注意要累加）去更新map，同时记录并更新所有edge中对应的最多砖块数即可。
- 因为对于某个edge来说，砖块数越多，那么沿着这个edge切分经过的砖块就越少。

```java
class Solution {
    public int leastBricks(List<List<Integer>> wall) {
        Map<Integer, Integer> map = new HashMap<>();
        int count = 0;
        for (List<Integer> row : wall) {
            int edge = 0;
            for (int i = 0; i < row.size() - 1; i++) {
                edge += row.get(i);
                map.put(edge, map.getOrDefault(edge, 0) + 1);
                count = Math.max(count, map.get(edge));
            }
        }
        return wall.size() - count;
    }
}
```