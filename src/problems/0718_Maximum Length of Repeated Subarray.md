# 718. Maximum Length of Repeated Subarray (2021.03.25)

https://leetcode.com/problems/maximum-length-of-repeated-subarray/

## Solution 1: DP ver1

- $TC:O(m*n)$
- $SC:O(m*n)$，可以进一步优化到O(min(m,n))，因为新的一行/一列的dp值只需要依赖于前一行/前一列。
- 要求两个数组的最大公共子数组的长度，就可以转换为求分别以i和j为起点的，A[i,...]和B[j,...]的公共前缀子数组的长度，即dp[i][j]。
- dp[i][j]满足当A[i]=B[j]时，dp[i][j] = dp[i+1][j+1] + 1。（当前位置A和B相等，公共前缀子数组向外延伸1个单位）
- 其中，dp[i][j]的最大值就是我们想要的最大公共子数组的长度。
- 同样地，我们也可以用记忆化递归进行top-down的求解。

```java
class Solution {
    public int findLength(int[] A, int[] B) {
        int m = A.length, n = B.length, res = 0;
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                if (A[i] == B[j]) {
                    dp[i][j] = dp[i + 1][j + 1] + 1;
                    res = Math.max(res, dp[i][j]);
                }
            }
        }
        
        return res;
    }
}
```

## Solution 2: DP ver2

- $TC:O(m*n)$
- $SC:O(m*n)$
- Solution1的是求最长的公共前缀子数组，我们也可以求最长的公共后缀子数组，即从0开始更新dp。

```java
class Solution {
    public int findLength(int[] A, int[] B) {
        int m = A.length, n = B.length, res = 0;
        int[][] dp = new int[m + 1][n + 1];
        
        // 为了便于处理边界情况，我们把i和j映射到i-1和j-1
        // 分别从1开始遍历，dp的左边和上边为初始边界状态
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (A[i - 1] == B[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                    res = Math.max(res, dp[i][j]);
                }
            }
        }
        
        return res;
    }
}
```

## Solution 3: Naive

- $TC:O(m*n*min(m,n))$
- $SC:O(min(m,n))$
- Naive的思路就是遍历A和B的所有pair(i,j)，其中i为A中子数组的起点，j为B中子数组的起点。
- 对每一组(i,j)，利用一个k从i和j开始逐位比较，直到超出A或B的边界范围或是A[i + k] != B[j + k]。
- 这时候判断当前最大值和k的大小，选出较大者。
- 由于一般情况下A[i]往往不等于b[j]，意味着我们无需遍历所有的(i,j)，只需要找到与A[i]相等的B[j]去遍历即可。
- 因此我们可以先遍历B获取每个值出现的index，再执行上述操作。

```java
class Solution {
    public int findLength(int[] A, int[] B) {
        int m = A.length, n = B.length;
        // 记录B中每个值的index
        Map<Integer, List<Integer>> bStarts = new HashMap<>();
        for (int i = 0; i < n; i++) {
            bStarts.putIfAbsent(B[i], new ArrayList<>());
            bStarts.get(B[i]).add(i);
        }
        
        int res = 0;
        for (int i = 0; i < m; i++) {
            // 只需要对与A[i]相等的B[j]进行遍历
            if (bStarts.containsKey(A[i])) {
                for (int j : bStarts.get(A[i])) {
                    int k = 0;
                    while (i + k < m && j + k < n && A[i + k] == B[j + k]) {
                        k++;
                    }
                    res = Math.max(res, k);
                }
            }
        }
        return res;
    }
}
```

## Solution 4: Naive + Binary Search

- $TC:O((m+n)*min(m,n)*log(min(m,n)))$
- $SC:O(m^2)$
- (m+n)*min(m,n)为每次执行check的时间复杂度，log(min(m,n))则为二分搜索的时间复杂度。
- 该解法的依据就是最终的max length必定不会超过m和n的较小值，因此我们可以对长度进行二分搜索。
- 过程就是根据l和r计算出mid，先判断mid长度下A和B是否存在相同的子数组。
- 如果存在，说明max length可能更长，把l设定为mid+1，探索更大的长度的可能性。
- 否则，说明max length要比mid还小，把r设定为mid（这里r指向的是探索过的或无效的index），探索更小的长度的可能性。

```java
class Solution {
    public int findLength(int[] A, int[] B) {
        int m = A.length, n = B.length;
        int l = 0, r = Math.min(m, n) + 1;
        while (l < r) {
            int mid = (r - l) / 2 + l;
            if (check(A, B, mid)) l = mid + 1;
            else r = mid;
        }
        return l - 1;
    }
    
    private boolean check(int[] A, int[] B, int k) {
        // 找出A中所有长度为k的子数组
        Set<String> subA = new HashSet<>();
        for (int i = 0; i + k <= A.length; i++) {
            subA.add(Arrays.toString(Arrays.copyOfRange(A, i, i + k)));
        }
        
        // 遍历B中所有长度为k的子数组，并检查其是否与A的某个子数组相同
        for (int j = 0; j + k <= B.length; j++) {
            String subB = Arrays.toString(Arrays.copyOfRange(B, j, j + k));
            if (subA.contains(subB)) return true;
        }
        
        return false;
    }
}
```