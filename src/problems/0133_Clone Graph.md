# 133. Clone Graph (2021.02.23)

https://leetcode.com/problems/clone-graph/

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> neighbors;
    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }
    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }
    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}
*/
```

## Solution 1: DFS

- $TC:O(N)$
- $SC:O(N)$
- 本题实质上就是图的遍历，不同的是在遍历过程中，如果遇到的是没有访问过的结点，则创建一个对应的clonedNode。
- 如果遇到的是之前访问过的结点，则需要从original结点到cloned结点的map中获取到clonedNode，然后添加edge。

```java
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) return null;
        
        // map不仅用于获得original node对应的cloned node
        // 同时还被用作visited，判断node是否已经被访问过
        Map<Node, Node> map = new HashMap<>();
        return dfs(node, map);
    }
    
    private Node dfs(Node node, Map<Node, Node> map) {
        // 当前node已经被访问过，返回其对应的copy
        if (map.containsKey(node)) {
            return map.get(node);
        }
        
        // 否则，创建一个copy并保存映射关系到map
        Node clonedNode = new Node(node.val);
        map.put(node, clonedNode);
        for (Node neighbor : node.neighbors) {
            // 把返回的copy添加到当前clonedNode的邻接结点中
            // 不需要反过来再添加一次，比如有a - b，在遍历a时会在对b的递归时把a添加到b的neighbors
            // 返回之后会把b添加到a的neighbors
            clonedNode.neighbors.add(dfs(neighbor, map));
        }
        
        return clonedNode;
    }
}
```

## Solution 2: BFS

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) return null;
        
        Node clonedNode = new Node(node.val);
        Map<Node, Node> map = new HashMap<>();
        map.put(node, clonedNode);
        
        Queue<Node> queue = new LinkedList<>();
        queue.add(node);
        while (!queue.isEmpty()) {
            Node curNode = queue.poll();
            for (Node neighbor : curNode.neighbors) {
                // neighbor还未被遍历过，创建neighbor对应的copy并添加到map和queue中
                if (!map.containsKey(neighbor)) {
                    Node neighborClone = new Node(neighbor.val);
                    map.put(neighbor, neighborClone);
                    queue.add(neighbor);
                }
                
                // 不论neighbor是否已经被遍历过，都要添加当前copy到neighbor copy的一条边
                // 同样地，如果neighbor被遍历过，那么neighbor copy到当前copy的边在之前就已经被创建好了
                // 否则，neighbor会在queue中等待后续的遍历，那时候也会将这条边创建起来
                map.get(curNode).neighbors.add(map.get(neighbor));
            }
        }
        
        return clonedNode;
    }
}
```