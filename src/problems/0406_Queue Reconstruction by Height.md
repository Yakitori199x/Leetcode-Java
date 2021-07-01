# 406. Queue Reconstruction by Height (2021.03.29)

https://leetcode.com/problems/queue-reconstruction-by-height/

## Solution 1: Sort and Insert

- $TC:O(n^2)$
- $SC:O(1)$
- 本题的思路是：首先对people进行排序，先是根据高度h（p[0]）从大到小排序，在h相同的情况下，根据k（p[1]）从小到大排序。
- 也就是说，先把最高的那一批人找出来，比如对于people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]，最高的两个人就是[7,0]和[7,1]。
- 且排序后的结果是people = [[7,0],[7,1],[6,1],[5,0],[5,2],[4,4]]。
- 不难发现，我们可以先将最高的人插入到最终的queue里，其对应的index就是p[1]，以上述为例，插入最高的两个人后queue = [[7,0],[7,1]]。
- 然后我们有[6,1]，可以看出我们必须将其插入到[7,0]和[7,1]之间，这个位置刚好就是[6,1]的第二个元素1。
- 因此，综上可知，在获得排序后的people之后，我们将其中的每一个元素依据p[1]作为index插入到最终的queue即可复原出正确的order。

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        if (people == null || people.length == 0 || people[0].length == 0) return null;
        
        Arrays.sort(people, (p1, p2) -> p1[0] == p2[0] ? p1[1] - p2[1] : p2[0] - p1[0]);
        
        List<int[]> queue = new ArrayList<>();
        for (int[] p : people) {
            queue.add(p[1], p);
        }
        return queue.toArray(new int[people.length][]);
    }
}
```