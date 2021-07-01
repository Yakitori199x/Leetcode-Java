# 1124. Longest Well-Performing Interval (2021.03.18)

https://leetcode.com/problems/longest-well-performing-interval/

## Solution 1: Prefix Sum + HashTable

- $TC:O(n)$
- $SC:O(n)$
- 本题求的是tiring的天数严格大于非tiring的天数的最大子数组。
- 如果我们用1来表示tiring，用-1来表示非tiring，那么问题实际上等价于求hours中sum大于0的最长子数组。

```java
class Solution {
    public int longestWPI(int[] hours) {
        int prefixSum = 0, res = 0;
        Map<Integer, Integer> seen = new HashMap<>();
        
        for (int i = 0; i < hours.length; i++) {
            // 遍历数组，并计算[0, i]的前缀和
            // 工作时长转化为1和-1来计算前缀和
            prefixSum += hours[i] > 8 ? 1 : -1;

            if (prefixSum > 0) {
                // 若前缀和大于0，说明[0, i]这一段子数组是符合要求的
                // 该子数组的长度就是当前最大长度
                res = i + 1;
            } else {
                // 如果前缀和小于或等于0，说明[0, i]这段子数组不符合要求
                // 但是我们可以把该前缀和首次出现的位置记录下来，以便之后使用
                seen.putIfAbsent(prefixSum, i);

                // 查看map中是否记录了前缀和为prefixSum-1时对应的位置
                // 为什么要求prefixSum-1呢？因为当前的前缀和为prefixSum，且prefixSum - (prefixSum-1) = 1。
                // 而两个前缀和的差其实就是它们之间这段子数组(pos(prefixSum-1), pos(prefixSum)]的sum。
                // 说明只要找得到prefixSum-1，那么中间这段子数组就是符合要求的。
                // 但是你可能会想？为什么不求prefixSum-2呢？这样它们之间的子数组之和为2，不是也满足需求吗？
                // 当然，找到prefixSum-2确实可以满足需求且对应子数组的sum更大，但是我们这里求的是最长的合理子数组。
                // 因为本题的每个位置对prefixSum只有+1和-1的操作，所以prefixSum-1必然在prefixSum-2出现，那么就可以构成更长的子数组。
                // 如果数组中的元素不再是+1和-1，那就可能出现prefixSum-2早于prefixSum-1出现的情况，那么此方法就不适用。
                if (seen.containsKey(prefixSum - 1)) {
                    res = Math.max(res, i - seen.get(prefixSum - 1));
                }
            }
        }
        
        return res;
    }
}
```

## Solution 2: Prefix Sum + Monotonic Stack

- $TC:O(n)$
- $SC:O(n)$
- 这类型题目更加通用的解法，可以适用于求sum大于k的最长子数组。
- 优化思路：
- 这种题目最naive的思路就是找到所有子数组(即所有i,j的对)，并且遍历每一个子数组以求出对应的sum，最后返回长度最大的满足条件的子数组。
- 第一种思路的时间复杂度会达到O(n^3)。
- 优化一：
- 这时候我们可以想到，有没有什么办法可以不需要遍历子数组就求出sum呢？答案就是利用前缀和了。
- 我们用prefix[i]记录数组[0, i)的前缀和（当然prefix[0]没有前缀和，设为0）。
- 那么prefix[i] - prefix[j] = a[j] + a[j+1] + ... + a[i-1]，就是[j, i-1]这段长为i-j的子数组的和。
- 这样一来，我们仍然是列举出所有i,j的对，但是只需要消耗O(1)的时间复杂度去判断sum是否符合要求，整个算法被优化到O(n^2)。
- 优化二：
- 我们可以进一步发现，对于某个j来说，存在i1和i2（i1 < i2)，如果prefix[i1] < prefix[i2]，我们只需要对i1进行判断。
- 这是为什么呢？因为如果i1符合条件，那么无论i2的前缀和是多少，[i1, j-1]肯定要比[i2, j-1]来得长。
- 如果i1不符合条件，那么由于prefix[i1] < prefix[i2]，i2更不可能符合条件。
- 相反，如果prefix[i1] > prefix[i2]，如果i1不符合条件，我们还是需要探索i2的可能性。
- 这个现象说明了我们无需列举出所有的i，只需要维护一个严格的单调递减栈来保存待探索的i的值，然后从i+1向右移动j来遍历这部分子数组即可。
- 优化三：
- 上述优化虽然排除了一些无用功，但时间复杂度并没有改变。因为对于stack中的每个i，我们还是要探索其右侧的所有的j。
- 那么我们可以想，是不是可以在j的部分做一些优化呢？实际上还真可以。
- 对于stack中的某个i来说，存在j1和j2（j1 < j2)，如果说j2已经满足条件了，那么对于当前的i来说就没有必要再探索更短的j1，直接探索下一个i即可。
- 这说明了对于每一个i来说，j不是从i+1开始，而是从n开始从右向左移动比较高效。
- 同时，假设我们从栈顶的i开始，由于stack是单调递减的（即当前i是前缀和最小的），如果对于i来说j2不满足条件，对于stack中其他的i来说，j2也没有探索的必要。
- 综合一下，就是我们可以从右到左移动j，并且比较prefix[j]和prefix[stack.peek()]的关系。
- 如果stack非空且prefix[j]大于prefix[stack.peek()]，说明它们对应的子数组符合条件。
- 那么就可以跳过stack.peek()的剩余遍历，将其弹出stack，对下一个栈顶元素进行判断。
- 如果prefix[j]小于或等于prefix[stack.peek()]，说明这一段子数组不符合条件。
- 那么当前的j对于stack中的剩余元素也无需再次检查，直接把j减1。如此反复直到j=0（其实stack空了的时候就什么都没做了）。

```java
class Solution {
    public int longestWPI(int[] hours) {
        int n = hours.length, res = 0;
        int[] prefixSums = new int[n + 1];
        // 构造前缀和数组
        for (int i = 1; i <= n; i++) {
            prefixSums[i] = prefixSums[i - 1] + (hours[i - 1] > 8 ? 1 : -1);
        }
        
        // 构造严格单调递减栈
        Deque<Integer> monoStack = new LinkedList<>();
        for (int i = 0; i <= n; i++) {
            if (monoStack.isEmpty() || prefixSums[monoStack.peek()] > prefixSums[i]) {
                monoStack.push(i);
            }
        }
        
        // 如果求的是子数组之和大于等于K的情况，则为while (!monoStack.empty() && prefixSums[monoStack.top()]+K <= prefixSums[j])
        for (int j = n; j >= 0; j--) {
            // 当然本题求的是sum大于0的情况
            while (!monoStack.isEmpty() && prefixSums[j] > prefixSums[monoStack.peek()]) {
                res = Math.max(res, j - monoStack.peek());
                monoStack.pop();
            }
        }
        
        return res;
    }
}
```