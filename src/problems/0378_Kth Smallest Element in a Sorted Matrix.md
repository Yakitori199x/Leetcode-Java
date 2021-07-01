# 378. Kth Smallest Element in a Sorted Matrix (2021.04.02)

https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/

## Similar Questions:
- 373. Find K Pairs with Smallest Sums

## Solution 1: Heap (Priority Queue)

- $TC:O(klogk)$
- $SC:O(k)$

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        Queue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        int n = matrix.length, cnt = 0, res = 0;
        for (int j = 0; j < n; j++) pq.offer(new int[]{matrix[0][j], 0, j});
        while (!pq.isEmpty()) {
            int[] entry = pq.poll();
            res = entry[0];
            int i = entry[1], j = entry[2];
            if (++cnt == k) break;
            if (i == n - 1) continue;
            pq.offer(new int[]{matrix[i + 1][j], i + 1, j});
        }
        return res;
    }
}
```

## Solution 2: Binary Search

- $TC:O(klog(max-min))$，max为matrix[0][0]，min为matrix[n-1][n-1]
- $SC:O(1)$
- 由于matrix的行和列都是单调递增的数列，我们可以很容易地发现matrix[0][0]为所有数的最小值，matrix[n-1][n-1]为所有数的最大值。
- 所以，要找到第k小的数，就是在min和max的范围进行二分搜索，每次根据mid在矩阵中所处的位置（即计算出当前mid是第几位的数）来更新搜索范围。
- 当然，在搜索的过程中mid不一定是存在于matrix的数，那么该解法为什么可以确保最终找到的lo一定在matrix中呢？
- 假设我们找到了一个不存在于matrix的值mid，有rank(mid)=k。那么我们可以肯定找到存在matrix中且比当前mid更小的值x满足rank(x)=k。
- 所以就算我们找到了这个mid，搜索也会继续下去并且收敛于rank为k的最小值，且该最小值一定在matrix中（因为如果再小，那么找到的值对应rank应该是k-1）。

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length, lo = matrix[0][0], hi = matrix[n - 1][n - 1] + 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            int i = 0, j = n - 1, cnt = 0;
            while (j >= 0 && i < n) {
                // 这里不能用matrix[i][j] >= mid去只计算比mid小的数，然后在下面把cnt加上1
                // 因为对于存在多个重复数值的情况，比如第7，8，9位均为13。
                // 假设我们要找的是k=8的情况，当mid=13时，最终获得的cnt=7，会导致lo一直被右移直到最后停在14
                if (matrix[i][j] > mid) {
                    j--;
                } else {
                    i++;
                    cnt += (j + 1);
                }
            }
            
            // 这里很关键，cnt >= k的情况确保了我们最终找到的lo一定是存在于matrix的
            if (cnt < k) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```