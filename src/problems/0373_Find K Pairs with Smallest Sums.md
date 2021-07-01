# 373. Find K Pairs with Smallest Sums (2021.04.02)

https://leetcode.com/problems/find-k-pairs-with-smallest-sums/

## Similar Questions:
- 378. Kth Smallest Element in a Sorted Matrix

## Solution 1: Heap (Priority Queue)

- $TC:O(klogk)$，更准确一点说可以控制第一次入队的循环，实现出O(klog(min(m, n, k)))的版本
- $SC:O(k)$
- 实际上nums1和nums2的和会形成m个（或n个）单调递增的数组，即nums1[0]和nums2的各个元素求和，nums1[1]和nums2的各个元素求和。。。
- 而这m个数组又可以组成一个行和列都是单调递增的矩阵，矩阵左上角的元素为最小的sum，右下角的元素为最大的sum。
- 我们首先将第一行的所有sum加入到pq，然后依次弹出pq的当前最小元素，直到获取了k个sum。
- 那么，我们在弹出pq的当前最小元素之后要如何判断加入什么新的候选呢？
- 不难发现，在弹出最小元素mat[i][j]后，mat[i+1][j]就是下一个需要加入pq的候选元素，除非i+1已经超出了范围。
- 那可能有人会问，为什么不加入mat[i][j+1]呢？这是因为mat[i][j+1]肯定大于或等于其上方的同一列的所有元素。
- 而这时候mat[i][j+1]上方的某个元素必然还在pq中，所以mat[i][j+1]必然不是下一个最小sum，不急于把其加入pq。

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        int m = nums1.length, n = nums2.length, cnt = 0;
        List<List<Integer>> res = new ArrayList<>();
        Queue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        
        for (int j = 0; j < n; j++) {
            pq.offer(new int[]{nums1[0] + nums2[j], 0, j});
        }
        
        while (!pq.isEmpty()) {
            int[] entry = pq.poll();
            int i = entry[1], j = entry[2];
            res.add(Arrays.asList(nums1[i], nums2[j]));
            if (++cnt == k) break;
            if (i == m - 1) continue;
            pq.offer(new int[]{nums1[i + 1] + nums2[j], i + 1, j});
        }
        
        return res;
    }
}
```