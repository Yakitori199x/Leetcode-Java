# 29. Divide Two Integers (2021.04.16)

https://leetcode.com/problems/divide-two-integers/

## Solution 1: Bit-manipulation, Long

- $TC:O(logn*logn)$
- $SC:O(1)$
- 由于本题不允许使用乘除运算来进行计算，我们可以从另外的角度来思考，除法实际上就是不断从被除数(dividend)中减去除数(divisor)，直到被除数小于或等于0。
- 当然，我们可以采取最简明的方式，每次只从被除数中减去一个除数，但这样做的效率偏低。
- 我们完全可以这么做，只要被除数仍旧大于或等于除数，就从m=0开始，把除数左移m位（*2^m），直到找到m使得左移后的结果超过被除数。
- 这时候我们就知道把除数左移m-1仍是在合理的范围内，所以我们可以把这部分的值从被除数中扣去，同时对商的部分加上1 << (m - 1)，相当于我们直接把2^m个除数从被除数中扣除。
- 不过这样还没有结束，剩余的被除数仍然可能大于除数，我们重复此过程直到被除数小于除数。
- 由此可知，inner-loop不断左移除数直到其大于被除数，时间复杂度为O(logn)（我们用n来表示被除数）。
- 同时，被除数在每次循环后最少会被扣除一半，所以outer-loop的时间复杂度也为O(logn)。综合内外循环，该解法的时间复杂度就是O(logn*logn)。
- 此处我们直接把int转换为long来避免计算过程中的溢出（MIN_VALUE）。
- 而且，对于dividend = MIN_VALUE且divisor = -1的情况下，最后的商仍旧会溢出，我们需要进行特殊处理。

```java
class Solution {
    public int divide(int dividend, int divisor) {
        if (dividend == Integer.MIN_VALUE && divisor == -1) return Integer.MAX_VALUE;
        // 这里在转换成绝对值之前，要先把int转换为long，否则仍然会溢出
        long divd = Math.abs((long) dividend), divs = Math.abs((long) divisor);
        int quotient = 0;
        while (divd >= divs) {
            long m = 0;
            // 找到最小的m使得除数左移m位后，超过当前被除数
            while ((divs << m) <= divd) m++;
            divd -= (divs << (m - 1));
            quotient += (1 << (m - 1));
        }
        // 判断被除数和除数是否同号，从而决定结果的符号
        return (dividend > 0) ^ (divisor > 0) ? -quotient : quotient;
    }
}
```

## Solution 2: Bit-manipulation, Int-only

- $TC:O(logn*logn)$
- $SC:O(1)$
- 该解法的思路和Solution1是一致的，不过我们不使用long来处理溢出情况，而是直接基于int去实现。

```java
class Solution {
    public int divide(int dividend, int divisor) {
        int quotient = 0;
        // 由于直接使用int去实现，对于dividend和divisor等于MIN_VALUE的情况都要特殊处理
        if (dividend == Integer.MIN_VALUE) {
            if (divisor == -1) return Integer.MAX_VALUE;
            if (divisor == Integer.MIN_VALUE) return 1;
            // dividend为MIN_VALUE，且divisor不为MIN_VALUE或-1的情况
            // 我们可以提前从dividend扣除一次divisor（这里表现为加上divisor的绝对值）并且将quotient加上1
            // 这样dividend就被处理成了一般的情况，且最终的quotient也能保持一致
            dividend += Math.abs(divisor);
            quotient += 1;
        }
        
        // 执行到这里说明dividend不可能是MIN_VALUE，那这时候结果必为0
        if (divisor == Integer.MIN_VALUE) return 0;
        int divd = Math.abs(dividend), divs = Math.abs(divisor);
        while (divs <= divd) {
            int temp = divs, m = 1;
            // temp在移位的过程中也可能溢出，我们需要在判断其移位后结果是否大于divd的同时
            // 判断其移位结果是否溢出到负数（因为每次都是*2，所以溢出后必不可能为正）
            while ((temp << 1) >= 0 && (temp << 1) <= divd) {
                temp <<= 1;
                m <<= 1;
            }
            divd -= temp;
            quotient += m;
        }
        
        return (dividend > 0) ^ (divisor > 0) ? -quotient : quotient;
    }
}
```