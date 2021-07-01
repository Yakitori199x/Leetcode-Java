# 901. Online Stock Span (2021.03.21)

https://leetcode.com/problems/online-stock-span/

```java
/**
 * Your StockSpanner object will be instantiated and called as such:
 * StockSpanner obj = new StockSpanner();
 * int param_1 = obj.next(price);
 */
```

## Similar Questions:
- 739. Daily Temperatures

## Solution 1: Monotonous Stack

- $TC:O(n)$
- $SC:O(n)$
- span的定义为当前股价及其左侧相连的小于或等于当前股价的天数。
- 换一个角度思考就是，找出当前股价左侧的大于当前股价的首天，那么这个范围就是span。
- 解决这类问题通常可以使用单调栈，这里我们维护了一个单调递减栈。
- 同时，因为最终求的是范围，我们需要把股价和span的pair存到stack里，初始状态为(price, 1)。

```java
class StockSpanner {
    
    private Deque<Pair<Integer, Integer>> stack;
    
    public StockSpanner() {
        this.stack = new LinkedList<>();
    }
    
    public int next(int price) {
        int span = 1;
        while (!stack.isEmpty() && stack.peek().getKey() <= price) {
            span += stack.pop().getValue();
        }
        stack.push(new Pair<>(price, span));
        return span;
    }
}

```