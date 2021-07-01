# 1562. Find Latest Group of Size M (2021.03.25)

https://leetcode.com/problems/find-latest-group-of-size-m/

## Solution 1: Counting

- $TC:O(n)$
- $SC:O(n)$
- 思路很简单，因为每一步操作都会将一个位置的0置为1，如果这个新的1周边已经存在1，那么就可以构成更大的group。
- 我们维护一个sizes来存储以sizes[i]为端点的group的长度（当然，随着不断更新和合并，有的点不再是端点，那么其对应的sizes也失去了本来的意义）。
- 然后维护一个counts来存储当前步骤下对应长度group的数量。
- 在每一步中，我们都会对位置idx进行操作，这时候我们只需要获取以idx+1和idx-1为端点的group的长度rightLen以及leftLen。
- 那么新组合成的group的长度就是mergedLen = leftLen + rightLen + 1。
- 由于从idx-leftLen到idx+rightLen形成了新的group，且这两个位置为该group的端点，我们需要更新这两个位置的sizes。
- 同时，由于合并，长度为leftLen和rightLen的group数量需要减少，而长度为mergedLen的group数量增加。
- 最后判断一下长度为m的group是否还存在，并更新latest step。

```java
class Solution {
    public int findLatestStep(int[] arr, int m) {
        int n = arr.length, res = -1;
        int[] counts = new int[n + 1], sizes = new int[n + 2];
        for (int step = 0; step < n; step++) {
            int idx = arr[step], leftLen = sizes[idx - 1], rightLen = sizes[idx + 1];
            int mergedLen = leftLen + rightLen + 1;
            sizes[idx - leftLen] = sizes[idx + rightLen] = mergedLen;
            counts[leftLen]--;
            counts[rightLen]--;
            counts[mergedLen]++;
            if (counts[m] > 0) res = step + 1;
        }
        return res;
    }
}
```

## Solution 2: Counting ver2 -- without counts array

- $TC:O(n)$
- $SC:O(n)$
- 对Solution1进行了一小点优化。关键思想在于我们不需要每一步都去维护counts。
- 假设直到第i步，长度为m的group依然存在（即第i+1步的合并会将其消灭），那么在第i步之前的合并过程，即使遇到的左右group都没有长度为m的group，没有对res进行更新，我们也不必担心。
- 因为在第i+1步时，合并操作的左group或是右group就会是我们要找的长度为m的group，这时候我们将res更新为i（即上一步）就是latest step。

```java
class Solution {
    public int findLatestStep(int[] arr, int m) {
        int n = arr.length, res = -1;
        // 由于我们是在merge后，查看上一步状态下的left和right是否等于m
        // 如果我们要的m恰好等于n，只靠以下的逻辑是找不出来的，因为最后一步合并获得长度为n的group后就结束了
        // 所以这里对其进行特殊处理
        if (m == n) return m;
        int[] sizes = new int[n + 2];
        for (int step = 0; step < n; step++) {
            int idx = arr[step], leftLen = sizes[idx - 1], rightLen = sizes[idx + 1];
            int mergedLen = leftLen + rightLen + 1;
            sizes[idx - leftLen] = sizes[idx + rightLen] = mergedLen;
            if (leftLen == m || rightLen == m) res = step;
        }
        return res;
    }
}
```

## Solution 3: Union-Find

- $TC:O(n)$
- $SC:O(1)$

```java
https://leetcode.com/problems/find-latest-group-of-size-m/discuss/806716/C%2B%2B-Union-Find-(Count-groups-of-size-Reverse-mapping)
```