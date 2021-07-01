# 974. Subarray Sums Divisible by K (2021.04.25)

https://leetcode.com/problems/subarray-sums-divisible-by-k/

## Similar Questions:
- 523. Continuous Subarray Sum

## Solution 1: HashMap

- $TC:O(n)$
- $SC:O(K)$
- 同样是关于sub-array sum的问题，我们很容易就可以想到O(n^2)的前缀和解法。
- 但是，我们还可以进一步优化。因为我们要求的就是存在多少对i，j满足(p[j] - p[i]) % K == 0。
- 而(p[j] - p[i]) % K == 0又可以等价于p[j] - p[i] = K * x（x为某个整数），同时mod K后可以得出p[j] % K == p[i] % K。
- 这说明对于当前的前缀和的p[j] % K，我们只需要找到有多少个之前的前缀和的p[i] % K与其相等即可。

```java
class Solution {
    public int subarraysDivByK(int[] A, int K) {
        Map<Integer, Integer> seen = new HashMap<>();
        int prefix = 0, count = 0;
        // 作为能够直接被K整除的子数组的base case
        seen.put(0, 1);
        for (int num : A) {
            // 这里也可以将mod和prefix分别存储，即prefix += num -> mod = prefix % K
            prefix = (prefix + num) % K;
            // 因为求出的余数可能是负数，比如-1 % 5，我们需要将其统一为非负余数
            // 也可以将上一行改为: prefix = (prefix + num % K + K) % K;
            if (prefix < 0) prefix += K;
            count += seen.getOrDefault(prefix, 0);
            seen.put(prefix, seen.getOrDefault(prefix, 0) + 1);
        }
        return count;
    }
}
```

## Solution 2: Array

- $TC:O(n)$
- $SC:O(K)$
- 由于Solution1中用于存储mod及其出现次数的Map，使用到的key不会超过K，因此用数组来保存会更有效率。

```java
class Solution {
    public int subarraysDivByK(int[] A, int K) {
        int[] seen = new int[K];
        int prefix = 0, count = 0;
        seen[0] = 1;
        for (int num : A) {
            prefix = (prefix + num) % K;
            if (prefix < 0) prefix += K;
            count += seen[prefix];
            seen[prefix]++;
        }
        return count;
    }
}
```