# 739. Daily Temperatures (2021.03.19)

https://leetcode.com/problems/daily-temperatures/

## Similar Questions:
- 901. Online Stock Span

## Solution 1: Monotonous Stack

- $TC:O(n)$
- $SC:O(n)$
- 和LC901题类似，只不过这题相当于求当前元素右侧的大于当前元素的首个元素。
- 还是使用一个单调递减栈，如果当前元素小于栈顶元素，直接入栈。
- 否则，说明当前元素是栈内部分元素的右侧首个大于其的元素。因此，将栈内元素出栈。
- 由于我们存储的是对应元素的index，我们要求的距离就是i-j，i为当前元素index，j为栈内元素index。

```java
class Solution {
    public int[] dailyTemperatures(int[] T) {
        Deque<Integer> stack = new LinkedList<>();
        int[] res = new int[T.length];
        for (int i = 0; i < T.length; i++) {
            while (!stack.isEmpty() && T[stack.peek()] < T[i]) {
                int j = stack.pop();
                res[j] = i - j;
            }
            stack.push(i);
        }
        return res;
    }
}
```