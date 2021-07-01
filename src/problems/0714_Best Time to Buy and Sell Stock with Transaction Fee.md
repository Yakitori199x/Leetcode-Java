# 714. Best Time to Buy and Sell Stock with Transaction Fee (2021.02.13)

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/

## Related Questions:
- 309. Best Time to Buy and Sell Stock with Cooldown

## Solution 1: DP

- $TC:O(N)$
- $SC:O(N)$，可以将空间继续优化到$O(1)$
- 将问题表示为sell和buy两种状态相互转换的状态机。
- sell[i]与buy[i]分别表示第i天处于sell状态（已卖出股票，可以买入股票）和buy状态（持有股票等待卖出）的最大利润。
- 相对于其他类似的股票买卖问题，该题增加了交易费的概念，所以需要在买入或是卖出的时候扣除相应的fee。
- 以下是基于买入时扣除交易费来实现的。

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        if (prices == null || prices.length <= 1) return 0;
        
        int n = prices.length;
        int[] sell = new int[n], buy = new int[n];
        
        sell[0] = 0;
        buy[0] = -prices[0] - fee;
        for (int i = 1; i < n; i++) {
            // keep the same as day i-1, or sell from buy status at day i-1
            sell[i] = Math.max(sell[i - 1], buy[i - 1] + prices[i]);
            // keep the same as day i-1, or buy from sell status at day i-1
            buy[i] = Math.max(buy[i - 1], sell[i - 1] - prices[i] - fee);
        }
        
        return sell[n - 1];
    }
}
```