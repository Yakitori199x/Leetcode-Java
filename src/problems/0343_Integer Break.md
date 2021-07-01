# 343. Integer Break (2021.06.04)

https://leetcode.com/problems/integer-break/

## Solution 1: Math ver1.

- $TC:O(n)$
- $SC:O(1)$
- 首先给出结论，对于大于或等于4的n（n可以是最初的数，也可以是分解后得到的因数），都必然可以进一步分解成多个2和3且获得更大的乘积，且优先分解出3直到剩余部分小于等于4（换句话说就是2的出现次数不超过2次）。
- 那么如何证明这一结论呢？我们假设存在一个数F >= 4，将其进一步分解成2和F-2，那么得到的乘积为2 * (F - 2) = 2 * F - 4 >= F。
- 这就说明了对于大于或等于4的数F来说，我们可以继续不断将其分解，直到所有的因数均为1，2或3，其中分解出1很明显无法获得更大的乘积。
- 那对于2和3是如何抉择的呢？不难发现，6可以被分解为3个2（乘积为8）或者两个3（乘积为9），所以只要存在3个2，那么将其替换成2个3的组合必然是能够使最终的结果更大的。
- 这也就说明了为什么我们需要优先分解出3且最终的2出现次数不超过两次。
- 还有一种证明方法就是，假设我们有一个数N且将其分成两部分，则有x * (N - x)。在这种情况下，如果N为偶数，则(N/2) * (N/2)为最大乘积。如果N为奇数，那么(N - 1) / 2 * (N + 1) / 2为最大乘积。
- 我们所期望的就是分解后的最大乘积能够大于或等于N，这样才有将N分解的价值。那么就有(N/2) * (N/2) >= N, 得到N >= 4。或者是(N - 1) / 2 * (N + 1) / 2 >= N, 得到N >= 5。
- 说明了对于所有的N >= 4，都应该继续对N进行分解以获得更大的乘积，因此所有分解出的因数必然为2或是3。

```java
class Solution {
    public int integerBreak(int n) {
        if (n == 2) return 1;
        if (n == 3) return 2;
        int prod = 1;
        while (n > 4) {
            n -= 3;
            prod *= 3;
        }
        return prod * n;
    }
}
```

## Solution 2: Math ver2.

- $TC:O(logn)$
- $SC:O(1)$
- Solution1中通过不断循环减去3得到结果，但其实没这个必要，直接求mod得出有多少个3，再做一个幂运算即可。

```java
class Solution {
    public int integerBreak(int n) {
        if (n == 2) return 1;
        if (n == 3) return 2;
        // pow为n可以分解出的3的个数，remainder为最后一个不足3的因数
        int pow = n / 3, remainder = n % 3;
        if (remainder == 1) {
            // remainder为1时，对应4，7，11，...这类情况
            // 即1 * 3 < 2 * 2，需要把多算的3扣掉，并将remainder设置为4
            pow -= 1;
            remainder = 4;
        } else if (remainder == 0) {
            // n能够被3整除，remainder设置为1
            remainder = 1;
        }
        return (int) Math.pow(3, pow) * remainder;
    }
}
```

## Solution 3: Memorization

- $TC:O(n^2)$
- $SC:O(n)$
- integerBreak(n)计算并返回分解n后能得到的最大乘积，那么就有integerBreak(n) = max(i * integerBreak(n - i))，其中1 <= i <= n。
- 注意对i = n的情况的处理，我们这里是用了一个flag来表示是否能在该情况下继续递归并返回n。

```java
class Solution {
    public int integerBreak(int n) {
        int[] memo = new int[n + 1];
        return integerBreak(n, true, memo);
    }
    
    private int integerBreak(int n, boolean first, int[] memo) {
        if (n == 0) return 1;
        if (memo[n] != 0) return memo[n];
        for (int i = 1; i <= n; i++) {
            // 如果n尚未被分解，那么在i = n的时候我们选择直接跳过，因为这样不符合必须将n分解为至少2个数的前提
            // 如果n已经是被分解后的数，那么我们是可以考虑直接返回n作为最终答案的因数的
            if (i == n && first) continue;
            memo[n] = Math.max(memo[n], i * integerBreak(n - i, false, memo));
        }
        return memo[n];
    }
}
```

## Solution 4: DP

- $TC:O(n^2)$
- $SC:O(n)$
- dp[i]表示i所对应的最大乘积，那么就有dp[i] = max(max(j, dp[j]) * max(i - j, dp[i - j]))。
- 为什么需要max(j, dp[j])和max(i - j, dp[i - j])？那是因为对于某些n来说，dp[n]比n本身来得小，比如n = 2，3。
- 所以我们在把i分解成j和i - j的情况下，还要看看是直接使用j或i - j去构成最终乘积呢，还是使用dp[j]或dp[i - j]去构成最终乘积。

```java
class Solution {
    public int integerBreak(int n) {
        int[] dp = new int[n + 1];
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int j = 1; j < i; j++) {
                dp[i] = Math.max(dp[i], Math.max(j, dp[j]) * Math.max(i - j, dp[i - j]));
            }
        }
        return dp[n];
    }
}
```