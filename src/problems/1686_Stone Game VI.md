# 1686. Stone Game VI (2021.05.19)

https://leetcode.com/problems/stone-game-vi/

## Solution 1: Greedy

- $TC:O(nlogn)$
- $SC:O(n)$
- 本题的意思是存在n堆stones，每堆stones对于A和B来说有不同的价值，且一旦某堆stones被A取走，那么B就不能够再取得这堆stones。
- 这就说明了玩家不能单纯选取当前对自己来说价值最大的stones，因为每次选择不仅是给自己增加价值，还可以使对手损失获取到这堆stones的价值的机会。
- 比如有A = [1,2], B = [3,1]，如果A按照始终选取当前最大的策略去行动，那么首先A会选择A[1]，然后B会选择B[0]，最终A的得分就会少于B的得分。但是，如果A选择的是A[0]，那么B就不得不选择B[1]，最终平局，对于A来说是更优的结果。
- 那么如果玩家不能采用单纯选取当前最大价值的策略，他们需要怎么做呢？首先我们可以来看只有2堆stones的情况，假设有A = [a1,a2], B = [b1,b2]，那么A总共有两种选法。
- 第一种是选择a1，然后让B选择b2，最终A与B的差值D = a1 - b2。第二种就是选择a2，然后让B选择b1，最终A与B的差值D = a2 - b1。
- 这样一来，a1 - b2和a2 - b1的大小就决定了哪一种选法为最优解。如果a1 - b2 = a2 - b1 -> a1 + b1 = a2 + b2，那么两种方法没有差别。如果a1 - b2 > a2 - b1 -> a1 + b1 > a2 + b2，那么优先选择第一堆stones。最后，如果a1 - b2 < a2 - b1 -> a1 + b1 < a2 + b2，那么优先选择第二堆stones。
- 不难看出，对于A来说，只要选择当前ai + bi（即stones对A和B的价值之和）最大的stones即可，这一结论可以推广到n > 2的情况去。
- 因此，我们可以求出所有si = ai + bi并对si从大到小进行排序，然后让A和B按顺序选择对应位置的stones并计算他们各自获得的价值，最后对结果进行比较即可。（这种选取方法对A来说是最优的，但并不意味着A一定能获胜，因为可能最优的选择只是尽量减少了A和B的差距）。
- 还有一种推导可见：https://leetcode.com/problems/stone-game-vi/discuss/969817/Strategy-proof （将D = a1 - b2转化为D = s1 - (b1 + b2)，D = a2 - b1转化为D = s2 - (b1 + b2)从而得出结论并推广到N的情况）

```java
class Solution {
    public int stoneGameVI(int[] aliceValues, int[] bobValues) {
        int n = aliceValues.length;
        int[][] sums = new int[n][2];
        for (int i = 0; i < n; i++) {
            sums[i] = new int[]{aliceValues[i] + bobValues[i], i};
        }
        
        Arrays.sort(sums, (a, b) -> Integer.compare(b[0], a[0]));
        int pickA = 0, pickB = 0;
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) pickA += aliceValues[sums[i][1]];
            else pickB += bobValues[sums[i][1]];
        }
        return Integer.compare(pickA, pickB);
    }
}
```