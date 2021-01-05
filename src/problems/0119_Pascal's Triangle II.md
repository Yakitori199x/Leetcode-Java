## 119. Pascal's Triangle II (2020.06.02)

https://leetcode.com/problems/pascals-triangle-ii/

## Solution 1

- $TC:O(k^2)$
- $TC:O(k)$
- Similar to 118. Pascal's Triangle, but only need to return one row of the whole triangle
- Keep one array to save the values of current row, then calculate values of next row in-place from end to front

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> row = new ArrayList<>();
        
        for (int i = 0; i < rowIndex + 1; i++) {
            row.add(1);
            for (int j = i - 1; j > 0; j--) {
                row.set(j, row.get(j - 1) + row.get(j));
            }
        }
        
        return row;
    }
}
```

