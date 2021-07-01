# 875. Koko Eating Bananas (2021.04.01)

https://leetcode.com/problems/koko-eating-bananas/

## Solution 1: Binary Search

- $TC:O(nlog(max(piles)))$
- $SC:O(1)$

```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int lo = 1, hi = 0, n = piles.length;
        if (n > h) return -1;
        for (int pile : piles) hi = Math.max(hi, pile);
        
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo, hours = 0;
            for (int pile : piles) {
                hours += (pile + mid - 1) / mid;
            }
            
            if (hours > h) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```