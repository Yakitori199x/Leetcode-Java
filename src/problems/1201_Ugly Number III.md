# 1201. Ugly Number III (2021.03.30)

https://leetcode.com/problems/ugly-number-iii/

## Solution 1: Binary Search + Venn Graph

- $TC:O(log(2e9))$
- $SC:O(1)$
- 本题与LC264. Ugly Number II不太一样，此处对于丑数的定义并不是所有的质因数必须是a，b，c中的一个或多个，而是只要能够被a，b，c中的一个整除即可。
- 比如a = 2，b = 3，c = 5的情况下，按照LC264的定义14 = 2 * 7不是丑数，但在本题中14就是一个合理的丑数。
- 因此，虽然本题也可以像LC264那样控制a，b，c不断生成新的丑数（只不过这里就是单纯的对a，b，c不断乘积下去），但是复杂度较高。
- 我们可以根据本题的条件进一步优化，假设给定一个数m，我们要如何确定小于等于m的范围内的丑数个数呢？
- 很简单，小于等于m的范围内，能够被a整除的数有m / a个，对于其他因数同理。
- 因此小于等于m的范围内，丑数的个数等于m / a + m / b + m / c - m / ab - m / bc - m / ac + m / abc。
- 其中ab，bc，ac，abc分别为a和b，b和c，a和c，以及a，b，c的最小公倍数（因为存在既能被a整除也能被b整除的数，这部分重复计算的要扣掉）。

```java
class Solution {
    public int nthUglyNumber(int n, int a, int b, int c) {
        // 由于会溢出，直接用long
        long lo = 1, hi = (long) 2e9 + 1;
        long A = a, B = b, C = c;
        long ab = (A * B) / gcd(A, B);
        long bc = (B * C) / gcd(B, C);
        long ac = (A * C) / gcd(A, C);
        // a，b，c三者的最小公倍数等于a和b，c的最小公倍数的最小公倍数（顺序可以换）
        long abc = (A * bc) / gcd(A, bc);
        
        while (lo < hi) {
            long mid = (hi - lo) / 2 + lo;
            long cnt = mid / a + mid / b + mid / c - mid /ab - mid / bc - mid / ac + mid / abc;
            if (cnt < n) lo = mid + 1;
            else hi = mid;
        }
        return (int) lo;
    }
    
    // 求最大公约数，要保证a小于b，则gcd(a, b) = gcd(b % a, a)直到计算出a等于0
    private long gcd(long a, long b) {
        if (a > b) return gcd(b, a);
        else if (a == 0) return b;
        else return gcd(b % a, a);
    }
}
```