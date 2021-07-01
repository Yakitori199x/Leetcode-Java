# 957. Prison Cells After N Days (2021.05.04)

https://leetcode.com/problems/prison-cells-after-n-days/

## Solution 1: HashMap + Find circular

- $TC:O(1)$
- $SC:O(1)$
- 这题比较巧妙，最直观的解法就是按照规则进行n次变换，但在n很大的情况下会超时。
- 此时，我们应该注意到cells的最左和最右两个位置在迭代的过程中必然会始终为0，因为它们没有同时拥有两个邻居。
- 因此，长度为8的cells实际上只有6个位置在不断变化，确定整个cells的状态。
- 所以很容易可以得出，不论经过多少天，cells的所有可能的状态为2^6=64种，那么在n很大的情况下必然会产生重复和循环。
- 综上，我们应该用map来记录每一种状态的出现时间，然后找出新得出的状态是否已经是之前得到过的状态。
- 如果是，那么说明当前得出的状态形成了一个循环，通过map记录的该状态上一次出现的时间就可以得到cycle的大小。
- 剩下的就是算出对于n来说，我们还需要变换多少次即可。
- 因为状态空间为64，因此cycle的大小必然不会超过这个范围，最终的TC和OC也与n无关，均为O(1)。

```java
class Solution {
    public int[] prisonAfterNDays(int[] cells, int n) {
        if (cells == null || cells.length == 0 || n == 0) return cells;
        
        Map<String, Integer> seen = new HashMap<>();
        int remain = 0;
        for (int i = 0; i < n; i++) {
            // 把cells转换为对应的String来表示当前的state
            // 如果直接把array存到map，那存的就是reference，不是cells的值
            String pattern = Arrays.toString(cells);
            if (seen.containsKey(pattern)) {
                int cycle = i - seen.get(pattern);
                remain = (n - i) % cycle;
                break;
            }
            seen.put(pattern, i);
            cells = getNext(cells);
        }
        
        for (int i = 0; i < remain; i++) cells = getNext(cells);
        return cells;
    }
    
    private int[] getNext(int[] cells) {
        int[] next = new int[cells.length];
        for (int i = 1; i < cells.length - 1; i++) {
            next[i] = cells[i - 1] == cells[i + 1] ? 1 : 0;
        }
        return next;
    }
}
```