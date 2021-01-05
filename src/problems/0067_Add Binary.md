## 67. Add Binary (2020.05.26)

https://leetcode.com/problems/add-binary/

## Solution 1

- $TC:O(max(len(a), len(b)))$
- $SC:O(1)$
- Iterate a and b from left to right
- Similar to [2. Add Two Numbers]

```java
class Solution {
    public String addBinary(String a, String b) {
        if (a == null) return b;
        if (b == null) return a;
        
        int i = a.length() - 1, j = b.length() - 1;
        int carry = 0;
        StringBuilder res = new StringBuilder();
        
        while (i >= 0 || j >= 0) {
            int sum = carry;

            // make the code compact
            // by computing the sum and moving the pointers together
            if (i >= 0) sum += (a.charAt(i--) - '0');
            if (j >= 0) sum += (b.charAt(j--) - '0');
            
            res.append(sum % 2);
            carry = sum / 2;
        }
        
        if (carry == 1) res.append("1");
        return res.reverse().toString();
    }
}
```

