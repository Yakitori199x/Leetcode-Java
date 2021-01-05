## 122. Best Time to Buy and Sell Stock II (2020.09.30)

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/

## Solution 1

- $TC:O(N)$
- $SC:O(1)$
- Continuously find the pair of local minimum and local maximum, then add their difference to final result

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) return 0;
        
        int maxProfit = 0;
        int localMin = prices[0], localMax = prices[0];
        int i = 1;
        
        while (i < prices.length) {
            while (i < prices.length && prices[i] <= prices[i - 1]) i++;
            localMin = prices[i - 1];
            
            while (i < prices.length && prices[i] > prices[i - 1]) i++;
            localMax = prices[i - 1];
            
            maxProfit += (localMax - localMin);
        }
        
        return maxProfit;
    }
}
```

## Solution 2: Greedy

- $TC:O(N)$
- $SC:O(1)$
- 只要遇到prices[i]大于prices[i-1]的情况，就将两数之差累加到profit上。最后就可以得到最大利润

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) return 0;
        
        int maxProfit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1]) {
                maxProfit += (prices[i] - prices[i - 1]);
            }
        }
        
        return maxProfit;
    }
}
```

