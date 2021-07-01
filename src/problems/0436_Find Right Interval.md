# 436. Find Right Interval (2021.04.16)

https://leetcode.com/problems/find-right-interval/

## Solution 1: TreeMap

- $TC:O(nlogn)$
- $SC:O(n)$
- 本题解法的关键在于把所有intervals的start及其对应的index保存在一个有序的数据结构里。
- 然后对于每一个interval，搜索是否存在start大于或等于interval的end，并返回其对应的index。（需要注意的是对于[2,2]这类start==end的情况，right interval是它本身。）
- 所以，一种方法是可以把start和index保存在一个List中，并且根据start进行排序，再用二分搜索求解。
- 不过这里我们直接使用了TreeMap来实现，map保存的entry是每个interval的start和index。
- TreeMap会根据key的值来保证新加入的entry的顺序，确保在查找的时候能够有O(logn)的效率。

```java
class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[] res = new int[n];
        TreeMap<Integer, Integer> map = new TreeMap<>();
        for (int i = 0; i < n; i++) map.put(intervals[i][0], i);
        
        for (int i = 0; i < n; i++) {
            Map.Entry<Integer, Integer> entry = map.ceilingEntry(intervals[i][1]);
            res[i] = entry == null ? -1 : entry.getValue();
        }
        
        return res;
    }
}
```