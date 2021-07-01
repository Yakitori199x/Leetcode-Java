# 1769. Minimum Number of Operations to Move All Balls to Each Box (2021.03.31)

https://leetcode.com/problems/minimum-number-of-operations-to-move-all-balls-to-each-box/

## Solution 1: DP

- $TC:O(n)$
- $SC:O(1)$
- 本题的思路是先从左到右遍历boxes，并且计算出位置i之前的每个球移动到位置i需要的移动次数ops。
- 然后再从右向左遍历boxes，进行相同的操作即可。

```java
class Solution {
    public int[] minOperations(String boxes) {
        int[] res = new int[boxes.length()];
        // count表示当前已知的球的个数，ops表示到达boxes[i]需要的移动次数
        // 移动到下一个位置的ops，就等于移动到当前位置的ops加上当前所有球的个数（所有的球移动在当前基础上移动一格即可）
        int count = 0, ops = 0;
        for (int left = 0; left < boxes.length(); left++) {
            res[left] += ops;
            count += boxes.charAt(left) == '1' ? 1 : 0;
            ops += count;
        }
        
        count = 0; ops = 0;
        for (int right = boxes.length() - 1; right >= 0; right--) {
            res[right] += ops;
            count += boxes.charAt(right) == '1' ? 1 : 0;
            ops += count;
        }
        return res;
    }
}
```