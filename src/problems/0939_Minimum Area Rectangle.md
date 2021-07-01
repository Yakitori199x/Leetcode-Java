# 939. Minimum Area Rectangle (2021.04.19)

https://leetcode.com/problems/minimum-area-rectangle/

## Solution 1: Map ver1.

- $TC:O(N^2)$
- $SC:O(N)$
- 我们只需要找到所有可能的对角线上的两个顶点，然后计算矩形面积即可。
- 当然，在遍历对角线上两个顶点时，我们需要判断是否存在另外两个对角线顶点，从而构成一个矩形。
- 这时候我们就可以利用map把每一行/每一列所对应的顶点的纵坐标/横坐标存储起来。
- 当我们遍历x和y两个顶点时，我们只需要检查y[1]是否在map[x[0]]中（即是否存在坐标为x[0],y[1]的点），以及x[1]是否存在于map[y[0]]（即是否存在坐标为y[0],x[1]的点）。

```java
class Solution {
    public int minAreaRect(int[][] points) {
        Map<Integer, Set<Integer>> map = new HashMap<>();
        for (int[] point : points) {
            if (!map.containsKey(point[0])) map.put(point[0], new HashSet<>());
            map.get(point[0]).add(point[1]);
        }
        
        int area = Integer.MAX_VALUE;
        // 从j=i+1开始遍历，可以排除一些重复的计算
        for (int i = 0; i < points.length; i++) {
            for (int j = i + 1; j < points.length; j++) {
                int[] x = points[i], y = points[j];
                // 如果x和y在同一行或是同一列，则不可能构成矩形
                if (x[0] == y[0] || x[1] == y[1]) continue;
                // 因为map和set的操作相比于计算面积更耗时间
                // 这里采用了先计算面积进行比较，如果得到的面积更小，再进行检查看是否构成合理的矩形的方法来优化
                int temp = Math.abs((x[0] - y[0]) * (x[1] - y[1]));
                if (temp >= area) continue;
                if (map.get(x[0]).contains(y[1]) && map.get(y[0]).contains(x[1])) {
                    area = Math.min(area, temp);
                }
            }
        }
        return area == Integer.MAX_VALUE ? 0 : area;
    }
}
```

## Solution 2: Map ver2.

- $TC:O(n)$
- $SC:O(1)$

```java
https://leetcode.com/problems/minimum-area-rectangle/discuss/192021/Python-O(N1.5)-80ms
```