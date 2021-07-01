# 50. Pow(x, n) (2021.04.12)

https://leetcode.com/problems/powx-n/

## Solution 1: Recursive

- $TC:O(logn)$
- $SC:O(logn)$
- 用递归的方法求解，根据当n为偶数时，有x^n = (x^2)^(n/2) -> pow(x, n) = pow(x*x, n/2)。
- 当n为奇数时，有x^n = x*(x^2)^(n/2) -> pow(x, n) = x * pow(x*x, n/2)。

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1;
        if (n < 0) {
            // 由于n可以取到最小整数值，如果直接反转为正数会产生溢出
            // 这里采取的是对于所有n小于0的情况，我们将n加上1后再反转为正
            // 这样我们求的就是pow(1/x, -(n+1))，并且将漏掉的那个1/x乘上去即可。
            return 1 / x * myPow(1 / x, - (n + 1));
        }
        
        if (n % 2 == 0) return myPow(x * x, n / 2);
        return x * myPow(x * x, n / 2);
    }
}
```

## Solution 2: Recursive ver2.

- $TC:O(logn)$
- $SC:O(logn)$

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1;
        // 直接把n等于最小整数值的情况拿出来整理
        // 如果n等于最小整数值，我们可以直接把n设置为原来的一半防止溢出，相应地将x设置为x * x。
        // 在之后的循环中再将n反转为正数，且将x设置为对应倒数。
        if (n == Integer.MIN_VALUE) {
            x = x * x;
            n = n / 2;
        }
        if (n < 0) {
            n = -n;
            x = 1 / x;
        }
        
        if (n % 2 == 0) return myPow(x * x, n / 2);
        return x * myPow(x * x, n / 2);
    }
}
```

## Solution 3: Bit Manipulation

- $TC:O(logn)$
- $SC:O(1)$
- 我们可以将n看作一串二进制序列，比如当n=5时，x^n = x^5 = x^(101) = x^(2^2 + 2^1) = x^4 * x^1
- 所以我们可以每次判断n的第一位二进制位是否为1，如果是1，就把当前对应的x乘到res上去。
- 当然，不论该二进制位是否为1，都需要将n右移一位（相当于除以2），且修改当前位对应的x。

```java
class Solution {
    public double myPow(double x, int n) {
        double res = 1, y = x;
        // 这里m和y都是为了应对n为最小整数值时溢出的情况
        // 当然也可以直接用long来存n，就可以直接用abs(n)
        int m = n < 0 ? - (n + 1) : n;
        while (m > 0) {
            if ((m & 1) == 1) res *= x;
            m >>= 1;
            x = x * x;
        }
        return n < 0 ? 1 / (y * res) : res;
    }
}
```