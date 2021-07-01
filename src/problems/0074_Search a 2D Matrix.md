# 74. Search a 2D Matrix (2021.04.11)

https://leetcode.com/problems/search-a-2d-matrix/

## Solution 1

- $TC:O(m+n)$
- $SC:O(1)$
- matrix实质上构成了一个每一行以及每一列都是单调递增数组的矩阵。
- 对于这类型的matrix，我们可以从右上角开始搜索。
- 如果matrix[i][j] = target，说明找到我们的目标。
- 如果matrix[i][j] < target，说明target必不可能在当前行（即第i行），直接进入i+1行搜索。
- 如果matrix[i][j] > target，说明target必不可能在当前列（即第j列），直接进入j-1列搜索。
- 在搜索时注意i和j不要越界即可。

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

## Solution 2: Binary Search, view as a list

- $TC:O(logmn)$
- $SC:O(1)$
- 因为matrix每一行的最后一个元素必定小于等于下一行的第一个元素，因此我们可以将matrix看做每一行头尾相连的一个list。
- 那么我们需要的就是对这个list进行二分搜索，当然我们不需要去创建这个list，只要能够得到list的index到matrix的index的映射即可。
- 这个映射关系就是n * i + j = idx -> i = idx / n, j = idx % n。

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length, n = matrix[0].length;
        int lo = 0, hi = m * n - 1;
        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            int mx = mid / n, my = mid % n;
            if (matrix[mx][my] == target) return true;
            else if (matrix[mx][my] < target) lo = mid + 1;
            else hi = mid - 1;
        }
        return false;
    }
}
```

## Solution 3: Binary Search, row then column

- $TC:O(logmn)$
- $SC:O(1)$
- 对于Solution2，如果matrix很大，m*n则可能会溢出。
- 因此我们可以采取另一种方法，先对第一列进行二分搜索，这样或许可以直接找到target，但肯定可以确定target可能位于的行。
- 锁定target所对应的行后，再对该行进行二分搜索，就可以确定最终结果。
- 当然，这个解法是在matrix每一行的最后一个元素必定小于等于下一行的第一个元素的情况下才成立。
- 如果某行的最后一个元素可能大于下一行的第一个元素，那么是无法通过这个方法来定位target可能位于的行的。

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length, n = matrix[0].length;
        int lo = 0, hi = m - 1;
        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            if (matrix[mid][0] == target) return true;
            else if (matrix[mid][0] < target) lo = mid + 1;
            else hi = mid - 1;
        }
        
        if (hi < 0) return false;
        
        int row = hi;
        lo = 0; hi = n - 1;
        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            if (matrix[row][mid] == target) return true;
            else if (matrix[row][mid] < target) lo = mid + 1;
            else hi = mid - 1;
        }
        return false;
    }
}
```