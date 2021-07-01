# 1139. Largest 1-Bordered Square (2021.05.07)

https://leetcode.com/problems/largest-1-bordered-square/

## Solution 1: DP ver1

- $TC:O(n^3)$
- $SC:O(n^2)$
- 因为我们想求的是四条边均为1的最大正方形，那么可以先构造vertical和horizontal来分别记录从上至下，从左至右，直到grid[i][j]处连续出现的1的个数。
- 比如，grid中存在某行为[1,0,1,1,1,1]，那么其对应的horizontal中的某行为[1,0,1,2,3,4]。这样一来，我们就可以把握grid中每个位置向左和向上可以延伸出多少个1的长度。
- 之后，我们可以从右下角的顶点开始，对grid中的每个点(i,j)，获取vertical和horizontal的较小者作为以(i,j)为右下顶点的满足条件的正方形的最大可能长度maxLen。
- 如果这个maxLen还不如当前的最大长度，则没有必要继续检查下去。否则，我们需要检查vertical[i][j - maxLen + 1]（正方形的左边）和horizontal[i - maxLen + 1][j]（正方形的上边）是否有足够的1来构成正方形。
- 当然，如果maxLen不满足条件，只要其还大于当前已知的最大长度，我们就要递减maxLen并继续检查下去。

```java
class Solution {
    public int largest1BorderedSquare(int[][] grid) {
        int m = grid.length, n = grid[0].length, res = 0;
        int[][] ver = new int[m][n], hor = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] > 0) {
                    ver[i][j] = (i == 0) ? 1 : ver[i - 1][j] + 1;
                    hor[i][j] = (j == 0) ? 1 : hor[i][j - 1] + 1;
                }
            }
        }
        
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int maxLen = Math.min(ver[i][j], hor[i][j]);
                while (maxLen > res) {
                    // (i, j - maxLen + 1)为左下角顶点，(i - maxLen + 1, j)为右上角顶点
                    if (ver[i][j - maxLen + 1] >= maxLen && hor[i - maxLen + 1][j] >= maxLen) {
                        res = maxLen;
                    }
                    maxLen--;
                }
            }
        }
        
        return res * res;
    }
}
```

## Solution 2: DP ver2

- $TC:O(n^3)$
- $SC:O(n^2)$
- 思路和Solution1一致，不过检查的时候最外层的循环为可能的最大长度。即从最大可能长度开始探索每一个点是否能成为满足条件的正方形的左上角顶点，直到长度为0。

```java
class Solution {
    public int largest1BorderedSquare(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] ver = new int[m][n], hor = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] > 0) {
                    ver[i][j] = (i == 0) ? 1 : ver[i - 1][j] + 1;
                    hor[i][j] = (j == 0) ? 1 : hor[i][j - 1] + 1;
                }
            }
        }
        
        for (int len = Math.min(m, n); len > 0; len--) {
            for (int i = 0; i < m - len + 1; i++) {
                for (int j = 0; j < n - len + 1; j++) {
                    // (i + len - 1, j)为左下角顶点，(i + len - 1, j + len - 1)为右下角顶点(i, j + len - 1)为右上角顶点
                    if (ver[i + len - 1][j] >= len && 
                        ver[i + len - 1][j + len - 1] >= len && 
                        hor[i][j + len - 1] >= len && 
                        hor[i + len - 1][j + len - 1] >= len) return len * len;
                }
            }
        }
        
        return 0;
    }
}
```