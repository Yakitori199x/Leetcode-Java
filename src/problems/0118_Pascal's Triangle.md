## 118. Pascal's Triangle (2020.06.01)

https://leetcode.com/problems/pascals-triangle/

## Solution 1: Dynamic programming

- $TC:O(n^2)$
- $SC:O(n^2)$
- The value of (i,j) equals to value of (i - 1, j - 1) plus (i - 1, j)
- dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j]

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> res = new ArrayList<>();
        
        for (int i = 0; i < numRows; i++) {
            List<Integer> row = new ArrayList<>();
            for (int j = 0; j < i + 1; j++) {
                if (j == 0 || j == i) row.add(1);
                else row.add(res.get(i - 1).get(j - 1) + res.get(i - 1).get(j));
            }
            res.add(row);
        }
        
        return res;
    }
}
```

