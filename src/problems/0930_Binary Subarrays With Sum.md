# 930. Binary Subarrays With Sum (2021.04.20)

https://leetcode.com/problems/binary-subarrays-with-sum/

## Solution 1: Map, Prefix Sum

- $TC:O(n)$
- $SC:O(n)$
- 由于本题要求子数组的和，我们很容易想到要应用前缀和。
- 最直观的想法就是，先遍历A获得每个index下的前缀和，然后用双指针去遍历所有的子数组。
- 但这种解法需要O(n^2)的时间复杂度，而且可以进一步优化。
- 其实，对于[i, j]这个范围的子数组，该子数组的元素和为prefixSum[j] - prefixSum[i - 1]，我们就是要看这个前缀和是否等于S。
- 但从另一个角度来看，我们是否有必要用双指针去遍历A呢？
- 其实是不需要的，我们完全可以在求出prefixSum[j]后，去检查之前的求得的前缀和中存在多少个值为prefixSum[j] - S的前缀和。
- 这说明我们只要求出每个位置的前缀和，同时把之前求得的前缀和及其出现次数保存起来就可以在一次遍历中求出答案。
- 这个解法中我们用Map来保存之前求出的前缀和及其次数，是一种较为通用的解法。

```java
class Solution {
    public int numSubarraysWithSum(int[] A, int S) {
        Map<Integer, Integer> preSum = new HashMap<>();
        // 如果前缀和刚好等于S，那么[0, i]这一段也是一个满足条件的子数组
        preSum.put(0, 1);
        int prefixSum = 0, res = 0;
        for (int i = 0; i < A.length; i++) {
            prefixSum += A[i];
            if (preSum.containsKey(prefixSum - S)) res += preSum.get(prefixSum - S);
            preSum.put(prefixSum, preSum.getOrDefault(prefixSum, 0) + 1);
        }
        return res;
    }
}
```

## Solution 2: Array, Prefix Sum

- $TC:O(n)$
- $SC:O(n)$
- 因为A中只存在0或1，所以最大前缀和不会超过n，即A的长度。
- 因此，我们不需要使用一个Map来记录前缀和，只需要使用一个数组即可，数组的index对应了前缀和的值。
- 其余思路和Solution1一致。

```java
class Solution {
    public int numSubarraysWithSum(int[] A, int S) {
        int[] preSum = new int[A.length + 1];
        preSum[0] = 1;
        int prefixSum = 0, res = 0;
        for (int i = 0; i < A.length; i++) {
            prefixSum += A[i];
            if (prefixSum >= S) res += preSum[prefixSum - S];
            preSum[prefixSum]++;
        }
        return res;
    }
}
```

## Solution 3: Sliding Window

- $TC:O(n)$
- $SC:O(1)$
- atMost(A, S)求的是A中子数组的元素和不超过S的子数组个数。
- 因此atMost(A, S) - atMost(A, S - 1)就是把和不超过S的子数组个数减去和不超过S-1的子数组的个数。
- 结果自然是和刚好为S的子数组的个数。
- 关于sliding window为什么可行的详细解析，可见（https://leetcode.com/problems/subarrays-with-k-different-integers/discuss/523136/JavaC++Python-Sliding-Window/556706）中nganhvo的comment
- 其中关于为什么诸如[a,b,c,d]对应的子数组个数就是1+2+3+4=10（即[0,j]，0 <= j < 4这段子数组的长度之和），我的理解是：通常我们可以用双指针来列举所有的子数组，那么不妨以外循环从i=0，内循环从j=i开始，每次循环递减j，内循环结束后递增i的模式来遍历，可以发现i=0时，子数组有1个，i=1时，子数组有2个，以此类推。
- 每次i增加1，新加入的元素带来的新子数组个数就是这段数组对应的长度。

```java
class Solution {
    public int numSubarraysWithSum(int[] A, int S) {
        return atMost(A, S) - atMost(A, S - 1);
    }
    
    private int atMost(int[] A, int S) {
        // 对于此题来说，子数组的和不可能出现负数，可以直接返回
        // 但是更通用的应该是修改下面的while循环为while (i <= j && sum > S)
        // 即每次遇到不符合条件的j，我们就缩小i到j的这段window
        // 若到i=j都没有遇到符合条件的window，最终res累加的就是0
        if (S < 0) return 0;
        int n = A.length, i = 0, sum = 0, res = 0;
        for (int j = 0; j < n; j++) {
            sum += A[j];
            while (sum > S) {
                sum -= A[i++];
            }
            res += (j - i + 1);
        }
        return res;
    }
}
```