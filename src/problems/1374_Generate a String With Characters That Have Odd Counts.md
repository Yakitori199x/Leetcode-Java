## 1374. Generate a String With Characters That Have Odd Counts (2020.03.09)

https://leetcode.com/problems/generate-a-string-with-characters-that-have-odd-counts/

## Solution 1

- $TC:O(N)$
- $SC:O(N)$
- Java one-line solution by using repeat(), repeat() is introduced in Java 11

```java
class Solution {
    public String generateTheString(int n) {
        return n % 2 == 0 ? "a".repeat(n - 1) + "b" : "a".repeat(n);
    }
}
```

## Solution 2

- $TC:O(N)$
- $SC:O(N)$
- Without repeat(), we can user StringBuilder

```java
class Solution {
    public String generateTheString(int n) {
        StringBuilder sb = new StringBuilder();
        
        if (n % 2 == 0) {
            for (int i = 0; i < n - 1; i++) sb.append("a");
            sb.append("b");
        } else {
            for (int i = 0; i < n; i++) sb.append("a");
        }
        
        return sb.toString();
    }
}
```

