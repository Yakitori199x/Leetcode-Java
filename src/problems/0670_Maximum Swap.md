# 670. Maximum Swap (2021.06.29)

https://leetcode.com/problems/maximum-swap/

## Solution 1: Find rightest largest num
- $TC:O(n)$
- $SC:O(n)$
- 要使得交换num的两位数字后得到的值最大，就是从高位开始，检查当前位置的右侧是否存在比当前位置的数来得大的数。
- 如果有，那么将当前位置的数值和最右侧的最大数值进行交换即可。
- 该解法是记录了num中每个数字最后出现的位置（越往右越后），然后从左往右遍历num的每一位数字进行上述检查。

```java
class Solution {
    public int maximumSwap(int num) {
        char[] digits = String.valueOf(num).toCharArray();
        int[] pos = new int[10];
        // 记录了num中每个数字最后出现的位置
        for (int i = 0; i < digits.length; i++) {
            pos[digits[i] - '0'] = i;
        }
        
        int maxDigit = 9;
        for (int i = 0; i < digits.length; i++) {
            // 这里可以对每一位数都始终从9开始检查，但是这么做会有很有不必要的计算
            // 该实现进行了一些优化，如果对于digits[i]没有找到其右侧存在更大的数
            // 那就说明对于下一位i+1来说，maxDigit必然不可能超过digits[i]
            for (int j = maxDigit; j > digits[i] - '0'; j--) {
                if (pos[j] > i) {
                    char temp = digits[i];
                    digits[i] = digits[pos[j]];
                    digits[pos[j]] = temp;
                    return Integer.valueOf(new String(digits));
                }
            }
            maxDigit = digits[i] - '0';
        }
        // 没有找到交换后获得更大值的机会，返回原数
        return num;
    }
}
```

## Solution 2: Right to Left - 2 pass

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int maximumSwap(int num) {
        char[] digits = String.valueOf(num).toCharArray();
        int n = digits.length, maxPos = n - 1;
        // maxRight[i]表示当前位置i（包括i）右侧大于或等于digits[i]的最大数的位置
        int[] maxRight = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            // 这里不能是大于或等于，不然不会记录下最右侧的最大数的位置
            if (digits[i] > digits[maxPos]) {
                maxPos = i;
            }
            maxRight[i] = maxPos;
        }
        
        for (int i = 0; i < n; i++) {
            if (digits[i] < digits[maxRight[i]]) {
                char temp = digits[i];
                digits[i] = digits[maxRight[i]];
                digits[maxRight[i]] = temp;
                return Integer.valueOf(new String(digits));
            }
        }
        return num;
    }
}
```

## Solution 3: Right to Left - 1 pass

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int maximumSwap(int num) {
        char[] digits = String.valueOf(num).toCharArray();
        int i = 0, j = 0, maxPos = digits.length - 1;
        // 从右向左遍历num的每一位，同时记录下当前位置及其右侧的最大数的位置
        // 如果发现当前的数小于当前最大数，说明k和maxPos是一个满足交换条件的candidate
        // 因此我们将其记录在i和j中，在遍历的过程中更新i和j，遍历结束时的i和j就是最优解
        for (int k = digits.length - 1; k >= 0; k--) {
            if (digits[k] > digits[maxPos]) {
                maxPos = k;
            }
            
            if (digits[k] < digits[maxPos]) {
                i = k;
                j = maxPos;
            }
        }
        
        char temp = digits[i];
        digits[i] = digits[j];
        digits[j] = temp;
        return Integer.valueOf(new String(digits));
    }
}
```