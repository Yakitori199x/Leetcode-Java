## 66. Plus One (2020.05.16)

https://leetcode.com/problems/plus-one/

## Solution 1

- $TC:O(n)$
- $SC:O(n)$ `[worst case]`
- Iterate from the end of the array, and see whether the digit is less then 9
- If it is, add 1 and return digits, no carry happens
- If not, set current digit as 0 and move to upper digit
- If the program does not end during iteration, the carry in most significant digit happens

```java
class Solution {
    public int[] plusOne(int[] digits) {
        for (int i = digits.length - 1; i >= 0; i--) {
            if (digits[i] < 9) {
                digits[i]++;
                return digits;
            }
            digits[i] = 0;
        }
        
        int[] res = new int[digits.length + 1];
        res[0] = 1;
        return res;
    }
}
```

