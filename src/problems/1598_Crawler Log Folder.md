# 1598. Crawler Log Folder (2021.04.07)

https://leetcode.com/problems/crawler-log-folder/

## Solution 1

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public int minOperations(String[] logs) {
        int steps = 0;
        for (String log : logs) {
            if (log.equals("./")) continue;
            else if (log.equals("../")) steps = Math.max(0, --steps);
            else steps++;
        }
        return steps;
    }
}
```