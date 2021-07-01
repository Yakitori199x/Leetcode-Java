# 464. Can I Win (2021.05.12)

https://leetcode.com/problems/can-i-win/

## Solution 1: DFS (Memo by String) + Backtracking

- $TC:O(2^n)$，如果是没有应用memorization的情况，则为O(n!)
- $SC:O(2^n)$
- 本题通过DFS+backtracking来确保玩家的每轮行动都是最优选择。
- 比如当玩家1选择了i+1(used[i]=1)，如果当前剩余的total已经不足i+1，则玩家1可以获得胜利，直接返回true到玩家2的上一轮次。
- 这时候玩家2就会注意到当前选择会使玩家1获胜，于是放弃当前选择的数，转而去判断选择下一个数能否获胜（寻找更优解）。
- 反之，如果当前剩余的total仍旧比i+1大，那么就进入到玩家2的回合，由其在used和total-(i+1)的状态下进行选择。
- 如果一直递归下去发现玩家2获胜，那么玩家1就会放弃选择当前的数，去探索后续的选择是否能获胜（寻找更优解）。
- 因为两个玩家都始终进行对自己最优的操作，最终获胜与否取决于当前剩余待选择的数used，因此我们可以用memorization来记录used对应的胜负结果，省去一些重复的计算。
- 简单来说，该解法并不是直接找到optimal的选择，而是一边枚举各种可能的选择，一边调整最优的选择。


```java
class Solution {
    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        int choosableTotal = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (desiredTotal <= 1) return true;
        if (choosableTotal < desiredTotal) return false;
        if (choosableTotal == desiredTotal) return maxChoosableInteger % 2 == 1;
        
        Map<String, Boolean> memo = new HashMap<>();
        int[] used = new int[maxChoosableInteger];
        return canIWin(used, desiredTotal, memo);
    }
    
    private boolean canIWin(int[] used, int total, Map<String, Boolean> memo) {
        String state = Arrays.toString(used);
        if (memo.containsKey(state)) return memo.get(state);
        
        for (int i = 0; i < used.length; i++) {
            if (used[i] != 0) continue;
            used[i] = 1;
            if (total <= i + 1 || !canIWin(used, total - (i + 1), memo)) {
                memo.put(state, true);
                used[i] = 0;
                return true;
            }
            used[i] = 0;
        }
        
        memo.put(state, false);
        return false;
    }
}
```

## Solution 2: DFS (Memo by Int) + Backtracking

- $TC:O(2^n)$
- $SC:O(2^n)$
- Solution1中用String作为memo的key还是比较耗时间的，会导致超时，因此我们可以用Int来作为memo的key。

```java
class Solution {
    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        int choosableTotal = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (desiredTotal <= 1) return true;
        if (choosableTotal < desiredTotal) return false;
        if (choosableTotal == desiredTotal) return maxChoosableInteger % 2 == 1;
        
        Map<Integer, Boolean> memo = new HashMap<>();
        return canIWin(0, maxChoosableInteger, desiredTotal, memo);
    }
    
    private boolean canIWin(int state, int max, int total, Map<Integer, Boolean> memo) {
        if (memo.containsKey(state)) return memo.get(state);
        
        for (int i = 0; i < max; i++) {
            // state相当于一个max位的bit sequence，这里就是在判断i+1是否已经被使用
            if ((state & (1 << i)) != 0) continue;
            // 若i+1尚未被使用，则通过state | (1 << i)来更新下一次递归的state
            if (total <= i + 1 || !canIWin(state | (1 << i), max, total - (i + 1), memo)) {
                memo.put(state, true);
                return true;
            }
        }
        
        memo.put(state, false);
        return false;
    }
}
```