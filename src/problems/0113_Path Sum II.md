## 113. Path Sum II (2020.09.12)

https://leetcode.com/problems/path-sum-ii/


```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
```

## Solution 1: DFS (Recursive)

- $TC:O(N)$
- $SC:O(N)$
如果考虑返回值，则为O(NlogN)【当树较为平衡且每条path都满足要求时】
- 这是一道back-tracking类型的题目，和【112. Path Sum】类似，重点在于tracking的对象由sum变成了path。由于Java是按值传递，所以要么在每次递归调用时传入新的object，要么在对应结点递归结束时将该结点的值移除。
- 如果每次递归调用都传入新的path list，那内存的开销将会到$O(n^2)$。所以选择及时remove的方式比较合理。

```java
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> paths = new ArrayList<>();
        List<Integer> path = new ArrayList<>();
        
        pathSum(root, sum, path, paths);
        return paths;
    }
    
    private void pathSum(TreeNode root, int sum, List<Integer> path, List<List<Integer>> paths) {
        if (root == null) return;
        
        path.add(root.val);
        
        if (root.left == null && root.right == null && root.val == sum) {
            paths.add(new ArrayList<>(path));
        } else {
            pathSum(root.left, sum - root.val, path, paths);
            pathSum(root.right, sum - root.val, path, paths);
        }
        
        path.remove(path.size() - 1);
    }
}
```

## Solution 2: Post-order

- $TC:O(N)$
- $SC:O(N)$, 如果考虑返回值，则为O(NlogN)【当树较为平衡且每条path都满足要求时】
- 其实前序，中序还有BFS都可以解决这个问题，但是都需要为每个结点维护一个pathSum以及到当前结点的path list。而后序遍历的遍历顺序刚好符合这个问题，我们就可以对整个遍历只创建一个pathSum和path，只需要注意在结点被遍历完成后进行删减即可。

```java
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> paths = new ArrayList<>();
        List<Integer> path = new ArrayList<>();
        Deque<TreeNode> nodes = new LinkedList<>();
        
        TreeNode cur = root, prev = null;
        int curSum = 0;
        while (cur != null || !nodes.isEmpty()) {
            while (cur != null) {
                nodes.push(cur);
                curSum += cur.val;
                path.add(cur.val);
                cur = cur.left;
            }
            
            cur = nodes.peek();
            if (cur.right != null && cur.right != prev) {
                cur = cur.right;
            } else {
                if (cur.left == null && cur.right == null && curSum == sum) {
                    paths.add(new ArrayList<>(path));
                }
                
                cur = nodes.pop();
                prev = cur;
                curSum -= cur.val;
                path.remove(path.size() - 1);
                cur = null;
            }
        }
        
        return paths;
    }
}
```

