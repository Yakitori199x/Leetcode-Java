# 274. H-Index (2021.04.13)

https://leetcode.com/problems/h-index/

## Solution 1: Counting Sort

- $TC:O(n)$
- $SC:O(1)$
- 首先要明确一个事实，即h-index的范围是0到n。
- 为什么不能超过citations的长度n呢？因为h-index的要求是满足至少有h篇论文，每篇论文的被引用量至少有h。
- 那么如果citations中只有n篇论文，那么h-index必不可能超过n。
- 在明白了这一点后，我们就可以去统计被引用量从0到n对应的论文数量了，其中被引数超过n的也归于n的bucket。
- 然后从n的bucket开始，累计在对应的h下有多少篇论文被引数大于等于h，找到的第一个h即为最大的h-index。
- 与桶排序不同的是，桶排序的每一个bucket一般是一个list，用于存放对应该bucket的所有元素，而这里我们只是进行计数，所以是一个特化版的桶排序，即counting sort。

```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] buckets = new int[n + 1];
        for (int citation : citations) {
            if (citation >= n) buckets[n]++;
            else buckets[citation]++;
        }
        
        int count = 0;
        for (int i = n; i >= 0; i--) {
            count += buckets[i];
            if (count >= i) return i;
        }
        return 0;
    }
}
```