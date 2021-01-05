## 166. Fraction to Recurring Decimal (2020.08.15)

https://leetcode.com/problems/fraction-to-recurring-decimal/

## Solution 1

- $TC:O(denominator + digits(numerator / denominator))$
- $SC:O(denominator)$, 因为余数必定比denominator要小，所以循环最多能到denominator次。对于TC，还需要考虑商的位数，因为有对stringbuilder的insert操作
- 这一题就是在模拟除法的过程，先确定整数部分的商，然后开始用余数迭代计算小数部分。
- 当得到的余数为0时，说明不存在循环。当得到的余数和之前的重复时，说明存在循环。
- 如果存在循环，我们就需要获得第一次计算出该余数的位置，因此我们需要一个hash table来保存余数及其index。
- 其他需要注意的就是两个计算数的符号问题以及overflow的问题。

```java
class Solution {
    public String fractionToDecimal(int numerator, int denominator) {
        if (numerator == 0) return "0";
        
        // 判断最终结果的符号
        StringBuilder res = new StringBuilder();
        if ((numerator > 0) ^ (denominator > 0)) res.append("-");
        
        // 统一转化为正数来计算，避免不必要的处理（比如商带有负号）
        // 同时因为int的最小值为-2147483648，直接转化为正数会溢出，因此用long来保存
        long num = Math.abs((long)numerator);
        long dem = Math.abs((long)denominator);
        
        res.append(num / dem);
        long rem = num % dem;
        if (rem == 0) return res.toString();
        
        res.append(".");
        Map<Long, Integer> map = new HashMap<>();
        map.put(rem, res.length());
        
        while (rem != 0) {
            res.append(10 * rem / dem);
            rem = (10 * rem) % dem;
            if (map.containsKey(rem)) {
                res.insert(map.get(rem), "(");
                res.append(")");
                break;
            } else {
                // 先向res加入本轮计算的商，然后再向map加入余数及其index
                // 也就是说，本轮的余数对应的index比商对应的index大1，循环数字也是从商后面一位开始的
                map.put(rem, res.length());
            }
        }
        
        return res.toString();
    }
}
```

