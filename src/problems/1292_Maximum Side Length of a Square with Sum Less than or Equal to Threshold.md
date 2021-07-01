# 1292. Maximum Side Length of a Square with Sum Less than or Equal to Threshold (2021.04.06)

https://leetcode.com/problems/maximum-side-length-of-a-square-with-sum-less-than-or-equal-to-threshold/

## Solution 1: Prefix Sum + Binary Search

- $TC:O(mnlog(min(m,n)))$
- $SC:O(mn)$
- 不难发现，可能的正方形边长的范围为[0, min(m,n)]，即最差的情况下不存在满足条件的正方形，但最大正方形的边长也不可能超过矩阵的较短边。
- 因此，很容易想到在[0, min(m,n)]进行二分搜索，如果在mid的情况下有满足条件的正方形，说明可以继续增大下次搜索的边长。
- 如果在mid的情况下不存在满足条件的正方形，说明要将下次搜索的边长范围减小。
- 那么，问题就在于我们要如何快速地求出在mid的情况下对应正方形的sum呢？这就需要用到前缀和，不过与数组前缀和不同的是，这里的前缀和是矩阵的前缀和。
- 我们用sum[i][j]来表示mat[0->i][0->j]这个范围的元素和，换句话说，就是以(0,0)为左上角顶点，(i,j)为右下角顶点的矩形的和。
- 利用这个sum以及对应的边长len，我们就可以快速求解出对应正方形的sum了。

```java
class Solution {
    public int maxSideLength(int[][] mat, int threshold) {
        int m = mat.length, n = mat[0].length;
        // 将sum[i][j]映射到mat[i-1][j-1]，便于处理边界情况
        int[][] sum = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                // 计算矩阵前缀和
                // (i,j)处矩形对应的sum = (i-1,j)处矩形对应的sum + (i,j-1)处矩形对应的sum - (i-1,j-1)处被重复计算的矩形对应的sum + (i-1,j-1)的矩阵元素值
                sum[i][j] = sum[i - 1][j] + sum[i][j - 1] - sum[i - 1][j - 1] + mat[i - 1][j - 1];
            }
        }
        
        int lo = 0, hi = Math.min(m, n);
        while (lo <= hi) {
            int mid = (hi - lo) / 2 + lo;
            if (isSquareExist(mid, threshold, sum)) {
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
        return hi;
    }
    
    private boolean isSquareExist(int len, int threshold, int[][] sum) {
        for (int i = len; i < sum.length; i++) {
            for (int j = len; j < sum[0].length; j++) {
                // 求以(i,j)为右下角顶点，边长为len的正方形的sum
                // sum[i - len][j - len]为sum[i - len][j]和sum[i][j - len]重叠部分，被扣除了两次，所以要加一次回来
                int tempSum = sum[i][j] - sum[i - len][j] - sum[i][j - len] + sum[i - len][j - len];
                if (tempSum <= threshold) return true;
            }
        }
        return false;
    }
}
```

## Solution 2: Prefix Sum + Sliding Window

- $TC:O(mn)$
- $SC:O(mn)$
- 相较于Solution1，该解法不需要在求出sum之后进行二分搜索。而是一边求sum，一边对可能的len进行判断，并且更新res。
- 可以发现，只要求出sum[i][j]，我们就可以在满足i >= len && j >= len的条件下求出对应len的正方形的sum。
- 而且，一旦在某个(i,j)处找到了边长为len的满足条件的正方形，在之后的遍历中我们就不需要再考虑边长小于等于len的所有情况，只需要对len+1进行判断即可。
- 比如我们从len=1开始，在某个(i,j)处找到了len为1的合理的正方形，那么在后续的遍历中我们直接考虑len=2的情况，因为这时候就算存在len=1的正方形也对结果没有影响。
- 那有人可能会问，为什么不在(i,j)确定len=1存在后，直接判断len=2呢？
- 那是因为在(i,j)处我们是首次找到合理的len=1的正方形，那么必定不存在len=2的合理解。
- 我们利用反证法，假设(i,j)存在len=2的解，那就说明在(i,j)之前的(i-1,j)，(i,j-1)或是(i-1,j-1)处就应该找到len=1的解，而不是到了(i,j)才首次找到对应解。

```java
class Solution {
    public int maxSideLength(int[][] mat, int threshold) {
        int m = mat.length, n = mat[0].length, len = 1, res = 0;
        int[][] sum = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                sum[i][j] = sum[i - 1][j] + sum[i][j - 1] - sum[i - 1][j - 1] + mat[i - 1][j - 1];
                if (i >= len && j >= len) {
                    int tempSum = sum[i][j] - sum[i - len][j] - sum[i][j - len] + sum[i - len][j - len];
                    if (tempSum <= threshold) {
                        res = len++;
                    }
                }
            }
        }
        
        return res;
    }
}
```