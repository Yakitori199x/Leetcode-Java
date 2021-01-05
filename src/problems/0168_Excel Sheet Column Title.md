## 168. Excel Sheet Column Title (2020.12.06)

https://leetcode.com/problems/excel-sheet-column-title/

## Solution 1

- $TC: O(logn)$
- $SC: O(logn)$
- 根据题设可以列出以下的对应关系：

| 1 | 2 | 3 | 4 | 5 | 6 |
| :----:| :----: | :----: | :----: | :----: | :----: |
| A   1 | AA    26+1 | BA  2×26+1 | ... | ZA  26×26+1 | AAA  1×26²+1×26+1 |
| B   2 | AB    26+2 | BB  2×26+2 | ... | ZB  26×26+2 | AAB  1×26²+1×26+2 |
| .   . | ..   ..... | ..  ...... | ... | ..  ....... | ...  ............ |   
| Z  26 | AZ    26+26| BZ  2×26+26| ... | ZZ  26×26+26| AAZ  1×26²+1×26+26|

- 可以发现本题实际上是一个将10进制转化为以1为起始的26进制数的转化问题。为了解决26这个corner case，我们可以在每次计算新的最低位时将n减去1。

```java
class Solution {
    public String convertToTitle(int n) {
        StringBuilder sb = new StringBuilder();
        while (n > 0) {
            int remainder = (--n) % 26;
            sb.append((char) ('A' + remainder));
            n = n / 26;
        }
        
        // Stringbuilder的insert方法cost是O(n)
        // 所以选择append后再reverse比较划算
        return sb.reverse().toString();
    }
}
```

