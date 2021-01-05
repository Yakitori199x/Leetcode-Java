## 299. Bulls and Cows (2020.08.23)

https://leetcode.com/problems/bulls-and-cows/

## Solution 1: One-pass hash

- $TC:O(N)$
- $SC:O(1)$
- 因为字符串只包含 `0~9` 这些数字，因此我们可以使用 `int[]` 来模拟 `Hashtable` 。数组的每个元素统计了该数字 `(index)` 在 `secret` 和 `guess` 中的出现情况。
- 同时遍历 `secret` 和 `guess` ，如果是位置 `i` 对应的是相同的数字，增加 `bull` 。如果是不同的数字，则检查 `int[]` 中两个数字的出现次数并判断是否增加 `cow`。最后，增加 `secretNum` 的计数，减少 `guessNum` 的计数（正向计数和反向计数）。

```java
class Solution {
    public String getHint(String secret, String guess) {
        int bulls = 0, cows = 0;
        int[] counts = new int[10];
        
        for (int i = 0; i < secret.length(); i++) {
            int secretNum = secret.charAt(i) - '0';
            int guessNum = guess.charAt(i) - '0';
            if (secretNum == guessNum) bulls++;
            else {
                if (counts[secretNum] < 0) cows++; // 说明guess在该位置之前有尚未配对的secretNum
                if (counts[guessNum] > 0) cows++; // 说明secret在该位置之前有尚未配对的guessNum
                counts[secretNum]++;
                counts[guessNum]--;
            }
        }
        
        return bulls + "A" + cows + "B";
    }
}
```

## Solution 2: Two-pass hash

- $TC:O(N)$
- $SC:O(1)$
- First pass for calculating the bulls and counting other numbers
- Second pass to calculate cows by summing the minimum counting of each number

```java
class Solution {
    public String getHint(String secret, String guess) {
        int bulls = 0, cows = 0;
        int[] secretCnt = new int[10];
        int[] guessCnt = new int[10];
        
        for (int i = 0; i < secret.length(); i++) {
            int secretNum = secret.charAt(i) - '0';
            int guessNum = guess.charAt(i) - '0';
            if (secretNum == guessNum) bulls++;
            else {
                secretCnt[secretNum]++;
                guessCnt[guessNum]++;
            }
        }
        
        for (int i = 0; i < secretCnt.length; i++) {
            cows += Math.min(secretCnt[i], guessCnt[i]);
        }
        
        return bulls + "A" + cows + "B";
    }
}
```

