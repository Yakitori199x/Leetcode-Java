# 752. Open the Lock (2021.03.09)

https://leetcode.com/problems/open-the-lock/

## Solution 1: BFS

- $TC:O(10^4)$，lock由四位数字组成，每位数字有10种可能，所有待探索的状态有10^4种
- $SC:O(10^4)$

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        // 将deadends对应的状态存储在一个set中，只需要O(1)判断开销
        Set<String> deads = new HashSet<>(Arrays.asList(deadends));
        Set<String> visited = new HashSet<>();
        // 初始状态就是deadend，直接结束
        if (deads.contains("0000")) return -1;
        
        Queue<String> queue = new LinkedList<>();
        queue.offer("0000");
        visited.add("0000");
        int res = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                String cur = queue.poll();
                if (cur.equals(target)) return res;
                
                StringBuilder sb = new StringBuilder(cur);
                for (int j = 0; j < 4; j++) {
                    char c = sb.charAt(j);
                    // 下一步可以转换的状态为：四个位，每一位两种状态（+1，-1）
                    String s1 = sb.substring(0, j) + (c == '9' ? 0 : c - '0' + 1) + sb.substring(j + 1);
                    String s2 = sb.substring(0, j) + (c == '0' ? 9 : c - '0' - 1) + sb.substring(j + 1);

                    if (!visited.contains(s1) && !deads.contains(s1)) {
                        queue.offer(s1);
                        visited.add(s1);
                    }
                    
                    if (!visited.contains(s2) && !deads.contains(s2)) {
                        queue.offer(s2);
                        visited.add(s2);
                    }
                }
            }
            res++;
        }
        
        return -1;
    }
}
```

## Solution 2: Bi-directional BFS

- $TC:O(10^4)$
- $SC:O(10^4)$
- 双向BFS，同时从起点和终点出发进行BFS，直到双方的维护的待遍历结点集合相交。
- 该解法并不是所有情况下都能优化时间的开销，最差的情况还是要遍历所有的状态（比如起点和终点就位于grid的两个对角）。
- 但是，除了某些情况外，双向BFS都拥有不错的优化。假定某个状态的相邻状态为8，并且探索N个level后才能找到target。
- 这种情况下，单向BFS的时间开销会达到O(8^N)。但是通过双向BFS，时间开销可以优化到O(2*8^(N/2))。

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        Set<String> deads = new HashSet<>(Arrays.asList(deadends));
        // begin和end对应了起点和终点的待遍历状态集合
        Set<String> begin = new HashSet<>();
        Set<String> end = new HashSet<>();
        if (deads.contains("0000")) return -1;
        
        begin.add("0000");
        end.add(target);
        Set<String> temp = new HashSet<>();
        int res = 0;
        while (!begin.isEmpty() && !end.isEmpty()) {
            // 这里是对双向BFS的进一步优化
            // 我们可以单纯地交错遍历begin和end
            // 但其实双向遍历的一个关键点就在于，单向的BFS可能会有某个level上存在很大的待遍历状态，这时候切换去遍历另一侧可能会少遍历一些状态
            // 所以我们这里始终选择较小的那一方进行BFS，即保证begin始终是待遍历状态较少的set
            if (begin.size() > end.size()) {
                temp = begin;
                begin = end;
                end = temp;
            }
            temp = new HashSet<>();
            for (String s : begin) {
                if (deads.contains(s)) continue;
                if (end.contains(s)) return res;
                deads.add(s);
                for (int i = 0; i < 4; i++) {
                    char c = s.charAt(i);
                    String s1 = s.substring(0, i) + (c == '9' ? 0 : c - '0' + 1) + s.substring(i + 1);
                    String s2 = s.substring(0, i) + (c == '0' ? 9 : c - '0' - 1) + s.substring(i + 1);
                    if (!deads.contains(s1)) temp.add(s1);
                    if (!deads.contains(s2)) temp.add(s2);
                }
            }
            begin = temp;
            res++;
        }
        
        return -1;
    }
}
```