## 985. Sum of Even Numbers After Queries (2020.10.25)

https://leetcode.com/problems/sum-of-even-numbers-after-queries/

## Solution 1

- $TC:O(m+n)$
- $SC:O(n)$
- $m$表示A的长度，$n$表示queries的数量。
- 这题可以划分为四种情况：
   1. even -> even，先减去A[idx]，修改后再加上A[idx]，相当于原来的A[idx]+val
   2. even -> odd，只减去A[idx]
   3. odd -> even，不减去A[idx]，修改后再加上A[idx]
   4. odd -> odd，什么都不做
- 综合以上四种情况，可以继续简化为：如果修改前为even，那么就减去该值。对该值进行修改后，如果为even，则直接加上修改后的值。

```java
class Solution {
    public int[] sumEvenAfterQueries(int[] A, int[][] queries) {
        int sum = 0;
        for (int num : A) {
            if (num % 2 == 0) sum += num;
        }
        
        int[] res = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int val = queries[i][0], idx = queries[i][1];
            
            if (A[idx] % 2 == 0) sum -= A[idx];
            A[idx] += val;
            if (A[idx] % 2 == 0) sum += A[idx];
            
            res[i] = sum;
        }
        
        return res;
    }
}
```

