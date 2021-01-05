## 55. Jump Game (2020.12.06)

https://leetcode.com/problems/jump-game/

## Solution 1: Greedy (left -> right)

- $TC: O(n)$
- $SC:O(1)$
- 贪心解法。从左至右遍历并记录下当前能够到达的最远位置。如果当前index已经大于最远位置，说明不管从之前的哪个位置都无法到达当前index，即可判断不可能到达终点。

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) return false;
        
        int farthest = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i > farthest) return false;
            farthest = Math.max(nums[i] + i, farthest);
        }
        
        // 能够顺利结束循环，说明farthest必定大于或等于最后一个index
        return true;
    }
}
```

## Solution 2: Greedy (right -> left)

- $TC: O(n)$
- $SC:O(1)$
- 贪心解法。从右至左遍历并记录能够到达终点的最小（最左）的index。每次迭代检查当前index是否能到达被记录的最小index，如果可以则对最小index进行更新。最后判断最小index是否等于0（初始位置）。

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) return false;
        
        int leftmost = nums.length - 1;
        for (int i = nums.length - 1; i >= 0; i--) {
            if (nums[i] + i >= leftmost) leftmost = i;
        }
        
        return (leftmost == 0) ? true : false;
    }
}
```

## Solution 3: DP

- $TC: O(n^2)$
- $SC:O(n)$
- dp解法。dp[i]表示从位置i是否能够到达终点，初始状态为dp[n-1]=true，即从终点处jump必定可以到达终点。接下去就是查看每个位置到其能够跳到的最远位置之间是否存在dp为true，如果存在，说明该位置也可以到达终点，将dp也改变为true。

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) return false;
        
        int n = nums.length;
        boolean[] dp = new boolean[n];
        
        dp[n - 1] = true;
        for (int i = n - 2; i >= 0; i--) {
            int farthestJump = Math.min(nums[i] + i, n - 1);
            for (int j = i + 1; j <= farthestJump; j++) {
                // 不需要check可跳跃区间内的所有位置
                // 只要遇到第一个可到达终点的位置，就可以判断当前位置也可以到达终点
                if (dp[j] == true) {
                    dp[i] = true;
                    break;
                }
            }
        }
        
        return dp[0];
    }
}
```

## Solution 4: Memorization Recursion

TBA

https://leetcode.com/problems/jump-game/solution/ (Approach 2)
