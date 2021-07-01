# 1482. Minimum Number of Days to Make m Bouquets (2021.03.29)

https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/

## Similar Questions:
- 1011. Capacity To Ship Packages Within D Days

## Solution 1: Binary Search

- $TC:O(n*log(maxDay))$
- $SC:O(1)$
- 思路和LC1011类似，当需要的bouquet数量m和每个bouquet包含的花朵数k的乘积超过了所有的花朵数时，不可能获取到我们想要的m个bouquet。
- 否则，我们可以知道，要获取m个bouquet，至少需要1天，至多需要maxDay（即bloomDay中最晚的那一天）。
- 因此我们可以在[1,maxDay+1)的范围做二分搜索，判断对应的天数mid下能否满足我们的需求。
- 当然，如果用更公式化的语言来解释的话，我们实际上是在对一个以天数为index，以当天获得的bouquet数为值的数组（这个数组是一个单调递增数组）进行二分搜索。
- 只不过这个值是即时计算出来的（最简单的情况就是直接获取数组的值）。

```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        int n = bloomDay.length;
        if (m * k > n) return -1;
        
        int maxDay = 0;
        for (int day : bloomDay) maxDay = Math.max(maxDay, day);
        int lo = 1, hi = maxDay + 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo, cur = 0, bouq = 0;
            for (int i = 0; i < n; i++) {
                if (bloomDay[i] <= mid) {
                    cur++;
                    if (cur == k) {
                        bouq++;
                        cur = 0;
                    }
                } else {
                    cur = 0;
                }
            }
            
            if (bouq < m) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```