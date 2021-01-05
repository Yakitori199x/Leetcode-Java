## 36. Valid Sudoku (2020.08.16)

https://leetcode.com/problems/valid-sudoku/

## Solution 1: HashSet + 坐标变换

- $TC:O(N^2)$
- $SC:O(N)$
- 在一个双层循环内同时检查row,column,block是否存在重复的数字
- 该循环原本是为了对每行进行检查，但是通过坐标转换可以同时对column和block进行检查
- row和column的坐标刚好是对称的，即(i, j) <-> (j, i)
- 而row和block的坐标映射需要一定的推倒，具体可以查看: https://leetcode.com/problems/valid-sudoku/discuss/15450/Shared-my-concise-Java-code的comment
- 简单来说就是通过j的变化来控制iBlock和jBlock的变化

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        if (board == null || board[0] == null) return false;
        
        int n = board.length;
        for (int i = 0; i < board.length; i++) {
            if (board[i] == null || board[i].length != n) return false;
        }
        
        for (int i = 0; i < n; i++) {
            Set<Character> setRow = new HashSet<>();
            Set<Character> setCol = new HashSet<>();
            Set<Character> setBlock = new HashSet<>();
            
            for (int j = 0; j < n; j++) {
                if (board[i][j] != '.' && !setRow.add(board[i][j])) return false;
                if (board[j][i] != '.' && !setCol.add(board[j][i])) return false;
                
                int iBlock = 3 * (i / 3) + (j / 3), jBlock = 3 * (i % 3) + (j % 3);
                if (board[iBlock][jBlock] != '.' && !setBlock.add(board[iBlock][jBlock])) return false;
            }
        }
        
        return true;
    }
}
```

## Solution 2

- $TC:O(N^2)$
- $SC:O(N^2)$
- 利用三个二维boolean数组来记录每行，每列，每个block出现过的数字，比如rows[0][1]代表在第一行里1是否已经出现过，blocks[2][1]代表第二个block中1是否出现过

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        if (board == null || board[0] == null) return false;
        
        int n = board.length;
        for (int i = 0; i < board.length; i++) {
            if (board[i] == null || board[i].length != n) return false;
        }
        
        boolean[][] rows = new boolean[n][n], cols = new boolean[n][n], blocks = new boolean[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] != '.') {
                    int num = board[i][j] - '0' - 1;
                    if (rows[i][num] || cols[j][num] || blocks[3 * (i / 3) + j / 3][num]) return false;
                    rows[i][num] = cols[j][num] = blocks[3 * (i / 3) + j / 3][num] = true;
                }
            }
        }
        
        return true;
    }
}
```

