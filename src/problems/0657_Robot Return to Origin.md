# 657. Robot Return to Origin (2021.02.27)

https://leetcode.com/problems/robot-return-to-origin/

## Solution 1

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public boolean judgeCircle(String moves) {
        if (moves == null || moves.length() == 0) return true;
        
        int x = 0, y = 0;
        for (int i = 0; i < moves.length(); i++) {
            char move = moves.charAt(i);
            if (move == 'U') y++;
            else if (move == 'D') y--;
            else if (move == 'R') x++;
            else if (move == 'L') x--;
        }
        
        return (x == 0) && (y == 0);
    }
}
```