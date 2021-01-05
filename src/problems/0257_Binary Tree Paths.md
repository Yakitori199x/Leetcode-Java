## 257. Binary Tree Paths (2020.09.12)

https://leetcode.com/problems/binary-tree-paths/


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

## Solution 1: DFS (Recursive) with String concatenation

- $TC:O(N^2)$
- $SC:O(N^2)$, 当树的所有结点构成一条链表时取到TC和SC的worst case
- 如果在树完全平衡的情况下，TC为$O(NlogN)$，SC为$O(NlogN)$
- 由于String是immutable的，所以当作参数传递时会产生一个新的String object。但是，这也导致了递归过程中进行多次String concatenation。

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> paths = new ArrayList<>();
        binaryTreePaths(root, "", paths);
        return paths;
    }
    
    private void binaryTreePaths(TreeNode root, String path, List<String> paths) {
        if (root == null) return ;
        
        path += root.val;
        if (root.left == null && root.right == null) {
            paths.add(path);
            return ;
        }
        
        binaryTreePaths(root.left, path + "->", paths);
        binaryTreePaths(root.right, path + "->", paths);
    }
}
```

## Solution 2: DFS (Recursive) with StringBuilder

- $TC:O(NlogN)$
- $SC:O(N)$
- StringBuilder可以很好地解决Solution1中的问题，我们只需要一个StringBuilder的对象，注意在对应结点的递归结束时对其进行删减即可。这样一来，我们只需要在遇到叶子结点时遍历StringBuilder中的元素并生成String。

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> paths = new ArrayList<>();
        StringBuilder path = new StringBuilder();
        binaryTreePaths(root, path, paths);
        return paths;
    }
    
    private void binaryTreePaths(TreeNode root, StringBuilder path, List<String> paths) {
        if (root == null) return ;
        
        int len = path.length();
        path.append(root.val);
        if (root.left == null && root.right == null) {
            paths.add(path.toString());
        } else {
            path.append("->");
            binaryTreePaths(root.left, path, paths);
            binaryTreePaths(root.right, path, paths);
        }
        
        path.setLength(len);
    }
}
```

## Solution 3: DFS (Recursive) with List

- $TC:O(NlogN)$
- $SC:O(N)$
- 和StringBuilder类似，不过使用的时List来存储path，同样也是只需要在遇到叶子结点的时候遍历生成字符串。

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> paths = new ArrayList<>();
        List<Integer> path = new ArrayList();
        
        binaryTreePaths(root, path, paths);
        return paths;
    }
    
    private void binaryTreePaths(TreeNode root, List<Integer> path, List<String> paths) {
        if (root == null) return ;
        
        path.add(root.val);
        
        if (root.left == null && root.right == null) {
            String temp = "";
            for (int i = 0; i < path.size(); i++) {
                if (i != 0) temp += "->";
                temp += path.get(i);
            }
            paths.add(temp);
        } else {
            binaryTreePaths(root.left, path, paths);
            binaryTreePaths(root.right, path, paths);
        }
        
        path.remove(path.size() - 1);
    }
}
```

## Solution 4: DFS (Recursive) -- Bottom-up

- $TC:O(N^2)$
- $SC:O(NlogN)$
- 自底向上生成paths的递归方法

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> paths = new ArrayList<>();
        if (root == null) return paths;
        
        if (root.left == null && root.right == null) {
            paths.add(root.val + "");
        }
        
        for (String path : binaryTreePaths(root.left)) {
            paths.add(root.val + "->" + path);
        }

        for (String path : binaryTreePaths(root.right)) {
            paths.add(root.val + "->" + path);
        }
        
        return paths;
    }
}
```

