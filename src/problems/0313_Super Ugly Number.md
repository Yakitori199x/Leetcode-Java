# 313. Super Ugly Number (2021.03.30)

https://leetcode.com/problems/super-ugly-number/

## Similar Questions:
- 264. Ugly Number II

## Solution 1: Math + DP

- $TC:O(n*k)$
- $SC:O(n+k)$
- 思路和LC264一样，只不过本题的质因数变成了k个，我们需要用一个数组来维护每个质因数当前对应的丑数。

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] res = new int[n];
        int[] idx = new int[primes.length];
        res[0] = 1;
        
        for (int i = 1; i < n; i++) {
            res[i] = Integer.MAX_VALUE;
            for (int j = 0; j < primes.length; j++) {
                res[i] = Math.min(res[i], res[idx[j]] * primes[j]);
            }
            
            for (int j = 0; j < primes.length; j++) {
                if (res[i] == res[idx[j]] * primes[j]) idx[j]++;
            }
        }
        
        return res[n - 1];
    }
}
```

## Solution 2: Math + DP (optimized)

- $TC:O(n*k)$
- $SC:O(n+k)$
- Solution1中用到了两次循环，而且有很多重复的计算，比如每次只有算出的最小的那个被确定，剩余的candidate在之后的循环中又要被重新计算。
- 该解法对这些点进行了改进。

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] res = new int[n];
        int[] idx = new int[primes.length];
        // 用cur来记录当前每个质因数对应能够生成的下一个丑数
        // 因为每次只有最小的那个值被赋值到res，然后对应的idx需要移动，其余的其实直到下一次循环都是保持不变的
        // 所以可以用额外使用一个数组来记录这些信息
        int[] cur = new int[primes.length];
        Arrays.fill(cur, 1);
        int next = 1;
        
        for (int i = 1; i <= n; i++) {
            // 每次循环计算出next丑数后并不急于赋值，在下一次循环进行赋值
            res[i - 1] = next;
            next = Integer.MAX_VALUE;
            for (int j = 0; j < primes.length; j++) {
                // 初始状态下由于cur[j]均为1，都会被更新
                // 之后就是上一轮循环中被用到的最小的一个或多个cur[j]
                if (cur[j] == res[i - 1]) cur[j] = res[idx[j]++] * primes[j];
                next = Math.min(next, cur[j]);
            }
        }
        
        return res[n - 1];
    }
}
```

## Solution 3: Priority Queue

- $TC:O(n*logk)$
- $SC:O(n+k)$
- 维护一个最小堆的优先队列，弹出当前的最小丑数作为next，然后将对应质因数的下一个结果加入优先队列。
- 为了能实现pq中始终只有k个candidates，我们加入队列的entry应当是[candidate ugly, prime, idx]。
- 这样的结构才能做到更新了下一个丑数的同时，根据prime和其对应的idx计算出这个质因数对应的next candidate。
- 剩下的就是注意处理duplicate的情况。

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        Queue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        int[] res = new int[n];
        res[0] = 1;
        for (int j = 0; j < primes.length; j++) {
            pq.offer(new int[]{primes[j], primes[j], 1});
        }
        
        int i = 1;
        while (i < n) {
            int[] entry = pq.poll();
            int next = entry[0], prime = entry[1], idx = entry[2];
            if (next != res[i - 1]) {
                res[i++] = next;
            }
            pq.offer(new int[]{res[idx] * prime, prime, idx + 1});
        }
        return res[n - 1];
    }
}
```