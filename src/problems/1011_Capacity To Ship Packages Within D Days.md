# 1011. Capacity To Ship Packages Within D Days (2021.03.26)

https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/

## Solution 1: Binary Search

- $TC:O(n * log(sum(weights) - max(weights)))$
- $SC:O(1)$
- 要确保货物能够被运输出去，船的载重量至少应该等于所有货物中重量最大的那个，这就是搜索的下界max(weights)。
- 此外，搜索的上界就是在一天的时间将所有货物运走，那么载重量就应该是所有货物的重量之和sum(weights)。
- 所以我们可以确定，我们想要求得的载重量肯定是落在[max(weights),sum(weights)]这个范围之内的。
- 我们可以对这个范围进行二分搜索，每次探索一个载重量的candidate，看看在这个candidate下需要多少天能够运完所有货物。
- 如果消耗的天数大于D，说明candidate略小了，下一步就在大于candidate的部分搜索。
- 否则，说明candidate恰好满足，或者是candidate太大了可以变小一点，那么就在范围的另一半进行搜索。

```java
class Solution {
    public int shipWithinDays(int[] weights, int D) {
        int lo = 0, hi = 1;
        for (int weight : weights) {
            lo = Math.max(lo, weight);
            hi += weight;
        }
        
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            int days = 1, cur = 0;
            for (int i = 0; i < weights.length; i++) {
                if (cur + weights[i] > mid) {
                    days++;
                    cur = 0;
                }
                cur += weights[i];
            }
            
            if (days > D) lo = mid + 1;
            else hi = mid;
        }
        
        return lo;
    }
}
```