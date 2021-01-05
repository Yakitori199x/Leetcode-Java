## 204. Count Primes (2020.08.06)

https://leetcode.com/problems/count-primes/

## Solution 1: Sieve of Eratosthenes(筛法求素数)

- $TC:O(nlog(logn))$, explanation in https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes
- $SC:O(n)$

```java
class Solution {
    public int countPrimes(int n) {
        int res = 0;
        boolean[] notPrimes = new boolean[n];
        // 只需要判断到i*i<n，因为在i*i>=n的情况下，i乘以从2开始到i-1的所有数都是已经判断过或者超出n的范围了
        for (int i = 2; i * i < n; i++) {
            if (notPrimes[i] == false) {
                int times = 2;
                while (i * times < n) {
                    notPrimes[i * times] = true;
                    times++;
                }
            }
        }
        
        for (int i = 2; i < n; i++) {
            if (!notPrimes[i]) res++;
        }
        return res;
    }
}
```

## Solution 2: 筛法求素数的一种很巧妙的变形

```java
class Solution {
    public int countPrimes(int n) {
        // 由于是求小于n的质数个数，所以当n小于3时（最大为2），不存在质数
        if (n < 3) return 0;
        
        boolean[] notPrimes = new boolean[n];

        // 由于除了2之外所有的偶数都是合数，所以小于n的范围内所有的质数都存在于除了1之外的所有奇数里（1和2抵消）
        // 我们可以设定有n/2个candidate，也意味着我们不需要筛选偶数，只需要关注奇数即可
        // 如果选中的奇数为合数，则减1
        int count = n / 2;

        // 只关注奇数 -> i+=2
        for (int i = 3; i * i < n; i += 2) {
            if (notPrimes[i]) continue;
            
            // 此处的j=i*i, j+=2*i
            // 实际上就是j=i*(i+2), i*(i+4), i*(i+6) ...
            // 因为i是奇数，与偶数相乘为偶数，没有筛选的必要。所以直接与大于i的下一个奇数相乘，比如i=3时，则检查3*3, 3*5, 3*7...
            // 这也是为什么从j=i*i开始遍历，因为小于i的奇数之前都检查过了
            for (int j = i * i; j < n; j += 2 * i) {
                // 有可能存在乘积相等的情况，如果不检查是否为合数，则会多减
                if (!notPrimes[j]) {
                    notPrimes[j] = true;
                    count--;
                }
            }
        }
        
        return count;
    }
}
```

