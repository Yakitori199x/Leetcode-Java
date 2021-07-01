# 309. Best Time to Buy and Sell Stock with Cooldown (2021.02.13)

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

## Solution 1: 2-states DP

- $TC:O(N)$
- $SC:O(N)$
- 本题的模型可以用sell和buy两个状态来描述。
- 其中，sell[i]表示到第i天为止，除去rest（即什么都不做），最后的操作是以卖出股票为结束（可能是第i天卖出，也可能是第i天之前的某天就卖出，之后什么都不做）的最大利润。
- buy[i]则表示到第i天为止，除去rest，整个操作以买入持有股票结束的最大利润。
- 那么，我们可以得到状态转移方程：sell[i] = max(sell[i-1], buy[i-1] + p[i])，buy[i] = max(buy[i-1], sell[i-2] - p[i])。
- 由于在卖出后（从buy状态到sell状态）需要cooldown一天，所以buy[i]的值与sell[i-2]有关。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;
        
        int n = prices.length;
        int[] sell = new int[n], buy = new int[n];
        
        sell[0] = 0;
        buy[0] = -prices[0];
        sell[1] = Math.max(sell[0], buy[0] + prices[1]);
        buy[1] = Math.max(buy[0], -prices[1]);
        
        for (int i = 2; i < n; i++) {
            sell[i] = Math.max(sell[i - 1], buy[i - 1] + prices[i]);
            buy[i] = Math.max(buy[i - 1], sell[i - 2] - prices[i]);
        }
        
        // 最大利润必定是在卖出股票的状态下
        return sell[n - 1];
    }
}
```

## Solution 2: 2-states DP (Optimized)

- $TC:O(N)$
- $SC:O(1)$
- 由于sell[i]只取决于sell[i-1]和buy[i-1]，buy[i]只取决于buy[i-1]和sell[i-2]，完全可以将dp数组优化到常数。
- 即分别用s0，s1，s2表示sell[i]，sell[i-1]和sell[i-2]。用b0，b1表示buy[i]和buy[i-1]。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;
        
        int n = prices.length, s0, b0;
        int s1 = 0, b1 = -prices[0], s2 = 0;
        
        for (int i = 1; i < n; i++) {
            s0 = Math.max(s1, b1 + prices[i]);
            b0 = Math.max(b1, s2 - prices[i]);
            s2 = s1;
            s1 = s0;
            b1 = b0;
        }
        
        return s1;
    }
}
```

## Solution 3: 3-states DP

- $TC:O(N)$
- $SC:O(N)$
- 还可以使用三个状态来为该问题建模使解决思路更明确。除了sell和buy，我们可以将cooldown的状态也独立出来，即rest。
- 那么，sell[i]可以从sell[i-1]休息一轮，或是从rest[i-1]转变而来。
- buy[i]可以从buy[i-1]休息一轮，也可以是从sell[i-1]买入股票转变而来。由于引入了rest状态，可以确保sell[i-1]已经是cooldown之后的状态了。
- 最后，rest[i]必然是由buy[i-1]卖出股票转变而来，且不存在不进行操作的选择，下一状态必然进入sell。
- 综上，有状态转移方程：sell[i] = max(sell[i-1], rest[i-1])，rest[i] = buy[i-1] + p[i]，buy[i] = max(buy[i-1], sell[i-1] - p[i])
- https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/discuss/75928/Share-my-DP-solution-(By-State-Machine-Thinking)

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;
        
        int n = prices.length;
        int[] sell = new int[n], buy = new int[n], rest = new int[n];
        
        sell[0] = 0; buy[0] = -prices[0]; rest[0] = Integer.MIN_VALUE;
        for (int i = 1; i < n; i++) {
            sell[i] = Math.max(sell[i - 1], rest[i - 1]);
            rest[i] = buy[i - 1] + prices[i];
            buy[i] = Math.max(buy[i - 1], sell[i - 1] - prices[i]);
        }
        
        return Math.max(sell[n - 1], rest[n - 1]);
    }
}
```

## Solution 4: 3-states DP (Optimized)

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;
        
        int n = prices.length;
        int s1 = 0, b1 = -prices[0], r1 = Integer.MIN_VALUE;
        int s0, b0, r0;
        
        for (int i = 1; i < n; i++) {
            s0 = Math.max(s1, r1);
            r0 = b1 + prices[i];
            b0 = Math.max(b1, s1 - prices[i]);
            s1 = s0;
            r1 = r0;
            b1 = b0;
        }
        
        return Math.max(s1, r1);
    }
}
```