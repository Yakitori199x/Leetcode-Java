# 264. Ugly Number II (2021.03.29)

https://leetcode.com/problems/ugly-number-ii/

## Solution 1: Math + DP

- $TC:O(n)$
- $SC:O(n)$
- 本题的关键在于：新的丑数肯定是由其之前的某个丑数乘以2，或者乘以3，或者乘以5得到的（即n = a * 2 or b * 3 or c * 5，其中a，b，c均为丑数）。
- 因此，我们的思路不应该是遍历每个数，然后去判断这个数是不是丑数（能不能分解成2，3，5的乘积）（这种方法开销很大，因为大部分数都不是丑数）。
- 而是根据已有的丑数序列去生成下一个丑数。那么要如何生成呢？
- 我们的起始状态必然是[1]，那么下一个丑数就是1 * 2，1 * 3和1 * 5中的最小者（就是2）。
- 当确定了第二个丑数是2后，我们能否直接根据2 * 2， 2 * 3以及2 * 5来判断下一个丑数呢？
- 答案是不行的，因为还有1 * 3和1 * 5两个candidate在等待判断（且它们必定小于2 * 3以及2 * 5）。
- 因此，我们可以维护三个指针p2，p3，p5，分别对应当前需要乘以2，乘以3和乘以5的丑数。
- 下一个丑数就是这三个乘积中的最小值，next = min(num[p2] * 2, num[p3] * 3, num[p5] * 5)。
- 计算出下一个丑数后，将对应乘积等于这个丑数的指针向左移动一格，使其指向下一个丑数以生成新的丑数。
- 需要注意的是，像6这样既可以是2 * 3，也可以是3 * 2的情况，我们需要对p2和p3都进行移动。

```java
class Solution {
    public int nthUglyNumber(int n) {
        if (n <= 0) return -1;
        int[] res = new int[n];
        int p2 = 0, p3 = 0, p5 = 0;
        res[0] = 1;
        for (int i = 1; i < n; i++) {
            res[i] = Math.min(res[p2] * 2, Math.min(res[p3] * 3, res[p5] * 5));
            if (res[i] == res[p2] * 2) p2++;
            if (res[i] == res[p3] * 3) p3++;
            if (res[i] == res[p5] * 5) p5++;
        }
        return res[n - 1];
    }
}
```

## Solution 2: Priority Queue

- $TC:O(nlogn)$
- $SC:O(n)$
- 从1开始对每一个新生成的丑数都分别乘以2，乘以3和乘以5，并把这些乘积的结果直接加入到最小堆的优先队列里。
- 优先队列会帮我们维护当前最小的丑数，也就是第i个丑数。
- 执行这样的过程n-1次即可获得第n个丑数（注意会产生溢出，需要使用Long）。

```java
class Solution {
    public int nthUglyNumber(int n) {
        if (n <= 0) return -1;
        Queue<Long> pq = new PriorityQueue<>();
        pq.offer(1L);
        
        for (int i = 1; i < n; i++) {
            long curUgly = pq.poll();
            // 去除重复的case
            while (!pq.isEmpty() && pq.peek() == curUgly) curUgly = pq.poll();
            
            pq.offer(curUgly * 2);
            pq.offer(curUgly * 3);
            pq.offer(curUgly * 5);
        }
        return pq.poll().intValue();
    }
}
```