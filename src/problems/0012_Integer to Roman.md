## 12. Integer to Roman (2020.05.11)

https://leetcode.com/problems/integer-to-roman/

## Solution 1

- $TC:O(logN)$, which is related to the digits of num
- $SC:O(logN)$, which is also related to the digits of num, 10x in general case
- Create mapping array for each digit manually, and do arithmetic

```java
class Solution {
    public String intToRoman(int num) {
        String[] M = {"", "M", "MM", "MMM"};
        String[] C = {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"};
        String[] X = {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"};
        String[] I = {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"};
        
        return M[num / 1000] + C[(num % 1000) / 100] + X[(num % 100) / 10] +I[num % 10];
    }
}
```

## Solution 2

- $TC:O(logN)$, which is related to the digits of num
- $SC:O(logN)$, which is also related to the digits of num, about 4x in general case
- Iterate through each possible roman

```java
class Solution {
    public String intToRoman(int num) {
        String[] romans = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        int[] nums = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        StringBuilder sb = new StringBuilder();
        
        for (int i = 0; i < romans.length; i++) {
            while (num >= nums[i]) {
                num -= nums[i];
                sb.append(romans[i]);
            }
        }
        
        return sb.toString();
    }
}
```

