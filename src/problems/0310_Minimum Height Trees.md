## 310. Minimum Height Trees (2020.11.04)

https://leetcode.com/problems/minimum-height-trees/

## Solution1: Topological Sort

- $TC:O(V)$
- $SC:O(V)$
- 本题用到了topological sort，先找到初始状态下所有最外层的leaves，然后将这些leaves及其对应的edges删除。在删除后有一些结点会成为新的leaves，重复之前的操作直到剩下不超过两个结点。那么，剩下的结点就是我们要求的MHT的root。
- 详细的解释可见: https://leetcode.com/problems/minimum-height-trees/solution/

```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        if (n == 1) return Collections.singletonList(0);
        
        // 构造邻接表，因为后续需要remove，所以选择Hashset
        List<Set<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new HashSet<>());
        
        for (int i = 0; i < edges.length; i++) {
            int n1 = edges[i][0], n2 = edges[i][1];
            adj.get(n1).add(n2);
            adj.get(n2).add(n1);
        }
        
        // 获得最初的leaves结点（degree = 1）
        List<Integer> leaves = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (adj.get(i).size() == 1) leaves.add(i);
        }
        
        int leftNodes = n;
        while (!leaves.isEmpty() && leftNodes > 2) {
            int size = leaves.size();
            List<Integer> nextLeaves = new ArrayList<>();
            leftNodes -= size;
            
            for (int i = 0; i < size; i++) {
                int node = leaves.get(i);
                for (int adjNode : adj.get(node)) {
                    adj.get(adjNode).remove(node);
                    if (adj.get(adjNode).size() == 1)
                        nextLeaves.add(adjNode);
                }
            }
            
            // 新加入的leaves将用于下一轮迭代
            // 或是当leaves不超过两个时作为返回值
            leaves = nextLeaves;
        }
        
        return leaves;
    }
}
```

## Solution2: Longest path

TBA

- https://leetcode.com/problems/minimum-height-trees/discuss/76052/Two-O(n)-solutions
- https://github.com/lydxlx1/LeetCode/blob/master/src/_310.java


## Solution3: Tree DP

TBA

- https://github.com/lydxlx1/LeetCode/blob/master/src/_310_1.java
