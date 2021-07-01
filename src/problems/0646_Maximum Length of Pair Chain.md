# 646. Maximum Length of Pair Chain (2021.02.14)

https://leetcode.com/problems/maximum-length-of-pair-chain/


## Solution 1: Backtracking (DFS)

- $TC:O(n*2^n)$，TLE
- $SC:O(n*2^n)$
- 此处的实现应该是没有错误的，但是复杂度太高超时了。
- 可以发现，对于这一类需要backtracking的问题，如果是要求得每一个具体的permutation/subset/pair chain，那么DFS和DP的复杂度是一样的。
- 但是，本题只需要求满足条件的candidates的最大长度，就很适合用dp来优化，因为此时dp不需要基于所有此前的candidates生成新的candidates集合。

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        List<List<Integer>> chain = new LinkedList<>();
        Arrays.sort(pairs, Comparator.comparingInt(e -> e[0]));
        int res = findLongestChain(pairs, 0, chain);
        return res;
    }
    
    private int findLongestChain(int[][] pairs, int index, List<List<Integer>> chain) {
        int res = 0;
        if (index >= pairs.length) return chain.size();
        
        for (int i = index; i < pairs.length; i++) {
            if (chain.size() == 0 || pairs[i][0] > chain.get(chain.size() - 1).get(1)) {
                List<Integer> temp = new ArrayList<>();
                temp.add(pairs[i][0]);
                temp.add(pairs[i][1]);
                chain.add(temp);
                res = Math.max(findLongestChain(pairs, i + 1, chain), res);
                chain.remove(chain.size() - 1);
            }
        }
        
        return res;
    }
}
```

## Solution 2: DP

- $TC:O(n^2)$
- $SC:O(n)$，dp[i]取决于之前的所有dp的值，所以不能优化到$O(1)$
- dp[i]表示从pairs[0]到pairs[i]的范围内，满足条件的pair chain的最大长度（以pairs[i]结束）。
- base case为dp[i] = 1。
- 因为可能存在如[[1,10], [2,5], ...]的情况，如果仅仅将dp[0]初始化为1，那么在当前实现下dp[1]就会是0，影响到后续的计算。

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        int n = pairs.length;
        int[] dp = new int[n];
        
        // 按pair的第一个元素升序排序，防止一些比较大的pair位于比较靠前的位置而没被计算进去的情况发生
        Arrays.sort(pairs, Comparator.comparingInt(e -> e[0]));
        Arrays.fill(dp, 1);
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                // dp[i]取决于dp[0]到dp[i-1]的maximum length of pair chains
                if (pairs[i][0] > pairs[j][1]) {
                    // 当前pair的第一个元素大于第j个pair的第二个元素时，说明可以加入形成更长的chain
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }
        
        return dp[n - 1];
    }
}
```

## Solution 3: Greedy

- $TC:O(nlogn)$
- $SC:O(1)$
- Greedy解法基于这样一个原理：当存在pairA和pairB用于构造chain时，我们倾向于向当前的chain加入tail较小的那一个pair。
- 即若pairA[1] < pairB[1]，那么我们会优先选择加入pairA。因为它的tail更小，意味着接下来更有可能加入更多的pairs，形成更长的chain。
- 因此，我们先将pairs根据tail的值从小到大排列，确保每次加入chain的都是当前能够被加入的pairs中tail最小的，这样我们就可以获得最长的chain。
- p.s. 需要注意的是，如果不满足the first number is always smaller than the second number，Greedy是会出错的。但是DP仍然可行。
- 比如Test case: [[1,3], [8,4], [3,5], [4,7]]

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        if (pairs == null || pairs.length == 0) return 0;
        
        Arrays.sort(pairs, Comparator.comparingInt(e -> e[1]));
        // cur表示chain的最后一个pair的tail，初始状态不存在chain，设定为最小负数int
        int res = 0, cur = Integer.MIN_VALUE;
        for (int i = 0; i < pairs.length; i++) {
            int head = pairs[i][0], tail = pairs[i][1];
            // 满足条件的pairs可以被加入，并且将cur设定到新的位置
            if (head > cur) {
                cur = tail;
                res++;
            }
        }
        
        return res;
    }
}
``｀｀