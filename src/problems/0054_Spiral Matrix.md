# 54. Spiral Matrix (2021.06.28)

https://leetcode.com/problems/spiral-matrix/

## Solution 1: Spiral Iteration

- $TC:O(m*n)$
- $SC:O(1)$
- sRow，eRow，sCol，eCol分别用于记录本轮遍历的上下左右边界，我们依据这些边界，每轮绕matrix遍历一圈。
- 同时根据res的size来判断是不是已经将matrix的所有元素都遍历过，防止重重复加入。

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix == null || matrix[0] == null) return res;
        
        int m = matrix.length, n = matrix[0].length;
        int sRow = 0, eRow = m - 1, sCol = 0, eCol = n - 1;
        int size = m * n;
        while (res.size() < size) {
            for (int i = sCol; i <= eCol && res.size() < size; i++) {
                res.add(matrix[sRow][i]);
            }
            
            for (int j = sRow + 1; j <= eRow - 1 && res.size() < size; j++) {
                res.add(matrix[j][eCol]);
            }
            
            for (int i = eCol; i >= sCol && res.size() < size; i--) {
                res.add(matrix[eRow][i]);
            }
            
            for (int j = eRow - 1; j >= sRow + 1 && res.size() < size; j--) {
                res.add(matrix[j][sCol]);
            }
            
            sRow++; eRow--; sCol++; eCol--;
        }
        
        return res;
    }
}
```

## Solution 2: Spiral Iteration ver2.

- $TC:O(m*n)$
- $SC:O(1)$
- 每当遍历完上边/右边/下边/左边，就在进入下一次边的遍历前检查sRow，eRow，sCol，eCol这几个边界是否满足条件。
- 同时，我们用switch和turn来决定每次进入的是哪一侧的边的遍历（总共只有四种情况），也可以说是用turn来决定遍历的方向。

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix == null || matrix[0] == null) return res;
        
        int m = matrix.length, n = matrix[0].length;
        int sRow = 0, eRow = m - 1, sCol = 0, eCol = n - 1, turn = 0;
        while (sRow <= eRow && sCol <= eCol) {
            switch(turn) {
                case 0:
                    for (int i = sCol; i <= eCol; i++) {
                        res.add(matrix[sRow][i]);
                    }
                    sRow++;
                    break;
                case 1:
                    for (int j = sRow; j <= eRow; j++) {
                        res.add(matrix[j][eCol]);
                    }
                    eCol--;
                    break;
                case 2:
                    for (int i = eCol; i >= sCol; i--) {
                        res.add(matrix[eRow][i]);
                    }
                    eRow--;
                    break;
                case 3:
                    for (int j = eRow; j >= sRow; j--) {
                        res.add(matrix[j][sCol]);
                    }
                    sCol++;
                    break;
            }
            turn = (turn + 1) % 4;
        }
        
        return res;
    }
}
```

## Solution 3: Direction

- $TC:O(n)$
- $SC:O(1)$

```java
https://leetcode.com/problems/spiral-matrix/discuss/20599/Super-Simple-and-Easy-to-Understand-Solution
jakwings
```