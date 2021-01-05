## 121. Best Time to Buy and Sell Stock (2020.09.28)

https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

## Solution 1: One-pass, continuously track minimum so far

- $TC:O(N)$
- $SC:O(1)$
- 因为只能进行一次交易且需要买了股票才能卖出，所以不能简单地找出prices的maximum和minimum。
- 但是，我们可以在遍历的同时记录当前的minimum，并根据该minimum计算出当前的最大利润。
- 如果遇到了更小的值，那么最大利润只可能是（当前的最大利润）或是（后续某日卖出价与此时的最小买入价之差），因此可以果断地重置最小值为prices[i]。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) return 0;
        
        int maxProfit = 0, minPrice = Integer.MAX_VALUE;
        for (int i = 0; i < prices.length; ++i) {
            if (prices[i] < minPrice) minPrice = prices[i];
            else maxProfit = Math.max(maxProfit, prices[i] - minPrice);
        }
        
        return maxProfit;
    }
}
```

## Solution 2: Turn to maximum subarray problem

- $TC:O(N)$
- $SC:O(1)$
- 可以转换为求相邻元素之差构成的数组的最大和连续子数组的问题。因为sum = prices[j] - prices[i]。
- 遍历prices的同时计算当前元素与前一元素之差（从1开始），就相当于在遍历相邻元素之差构成的数组。
- 因为我们要求的是最大和，所以当sum变成负数的时候直接设置为0即可。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) return 0;
        
        int maxProfit = 0, curMax = 0;
        for (int i = 1; i < prices.length; i++) {
            curMax = Math.max(0, curMax + prices[i] - prices[i - 1]);
            maxProfit = Math.max(maxProfit, curMax);
        }
        
        return maxProfit;
    }
}
```

