# 240. Search a 2D Matrix II (2021.04.11)

https://leetcode.com/problems/search-a-2d-matrix-ii/

## Similar Questions:
- 74. Search a 2D Matrix

## Solution 1

- $TC:O(m+n)$
- $SC:O(1)$
- 该解法其实就是LC74的Solution1。
- 至于为什么LC74的Solution2和Solution3不再适用于此题，是因为本题中的matrix虽然也是每行以及每列都是单调递增数组。
- 但是无法保证每一行的末尾元素一定小于等于下一行的首元素。

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int i = 0, j = matrix[0].length - 1;
        while (i < matrix.length && j >= 0) {
            if (matrix[i][j] == target) return true;
            else if (matrix[i][j] > target) j--;
            else i++;
        }
        return false;
    }
}
```