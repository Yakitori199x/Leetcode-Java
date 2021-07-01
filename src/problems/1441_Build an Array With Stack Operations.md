# 1441. Build an Array With Stack Operations (2021.04.09)

https://leetcode.com/problems/build-an-array-with-stack-operations/

## Solution 1: Simulating

- $TC:O(n)$
- $SC:O(1)$
- 由于target是严格递增的，确保了一定有解。
- 否则，如果target中出现递减或者相等的情况，则不存在可行解。

```java
class Solution {
    public List<String> buildArray(int[] target, int n) {
        int ptr = 0, m = target.length;
        List<String> res = new ArrayList<>();
        for (int i = 1; i <= n && ptr < m; i++) {
            res.add("Push");
            if (target[ptr] == i) {
                ptr++;
            } else {
                res.add("Pop");
            }
        }
        return res;
    }
}
```