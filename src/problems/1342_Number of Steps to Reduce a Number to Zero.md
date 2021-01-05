## 1342. Number of Steps to Reduce a Number to Zero (2020.11.08)

https://leetcode.com/problems/number-of-steps-to-reduce-a-number-to-zero/

## Solution1: Bit manipulation

- $TC:O(log(num))$
- $SC:O(1)$

```java
class Solution {
    public int numberOfSteps (int num) {
        // 当num=0时需要特殊处理，直接返回0
        if (num == 0) return 0;
        
        int res = 0;
        
        while (num != 0) {
            // 如果当前num为偶数，只需要进行除操作，res加1
            // 如果当前num为奇数，需要进行减操作和除操作，res加2
            // 然后将当前num右移1位
            res += (num & 1) == 0 ? 1 : 2;
            num >>= 1;
        }
        
        // 当遇到left-most的1时，只需要进行减操作而不需要再进行除操作了
        // 这里将while里多计算的一次操作减去
        return res - 1;
    }
}
```

## Solution2: Bit Counting

- $TC:O(log(num))$
- $SC:O(1)$

```java
class Solution {
    public int numberOfSteps (int num) {
        if (num == 0) return 0;
        // steps等于num二进制表示中所有1的个数+所有位数
        // Integer.bitCount(num)计算num中值为1的位数
        // Integer.numberOfLeadingZeros(num)获取leading zeros的个数，即left-most 1左侧的值为0的位数
        return Integer.bitCount(num) + (31 - Integer.numberOfLeadingZeros(num));
    }
}
```

