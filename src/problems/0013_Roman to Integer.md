## 13. Roman to Integer (2020.03.20)

https://leetcode.com/problems/roman-to-integer/

## Solution 1

- $TC:O(n)$, `n is length of the Roman number`
- $SC:O(1)$
- If the value of next symbol is larger than that of current symbol, do substract. Always add the value of final symbol.

```java
class Solution {
    public int romanToInt(String s) {
        Map<Character, Integer> map = new HashMap<>();
        map.put('I', 1);
        map.put('V', 5);
        map.put('X', 10);
        map.put('L', 50);
        map.put('C', 100);
        map.put('D', 500);
        map.put('M', 1000);
        
        int res = 0;
        for (int i = 0; i < s.length() - 1; i++) {
            int value = map.get(s.charAt(i));
            if (value >= map.get(s.charAt(i + 1))) res += value;
            else res -= value;
        }
        
        return res + map.get(s.charAt(s.length() - 1));
    }
}
```

