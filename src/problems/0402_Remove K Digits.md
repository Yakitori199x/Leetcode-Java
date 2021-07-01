# 402. Remove K Digits (2021.03.12)

https://leetcode.com/problems/remove-k-digits/

## Solution 1: Greedy + Stack

- $TC:O(n)$
- $SC:O(n)$
- Naive的思路就是每次通过遍历字符串找出当前最大的数，然后移除这个数，将这个过程反复执行k次即可。
- 但是这种思路的时间复杂度会到达O(k*n)。因此，我们可以换一个角度来思考。
- 我们的目的是从num中移除k个数字，使得最终得到的(n-k)个数字组成的数最小。
- 那么可以发现，我们从左到右遍历num，只要num[i]的值比之前已经遍历过的位置的数字来得小，我们就没有必要保留前面的数字。
- 比如num=456123，我们发现1比其前面的4，5和6都要小，我们要在移除k=2位后得到最小数，必然不需要保留5和6（由于k的限制不能继续移除4）。
- 而是以1作为第二位数才可以取得更小的值。这就是贪心的思想，在遍历过程中的每一步操作都是为了取得离目标更近的结果（此处就是更小的值）。

```java
class Solution {
    public String removeKdigits(String num, int k) {
        if (num == null || num.length() == 0) return num;
        
        int n = num.length(), i = 0;
        if (n == k) return "0";
        Deque<Character> stack = new LinkedList<>();
        while (i < n) {
            char c = num.charAt(i);
            // 注意需要用while，把遍历过的大于当前数字的数都弹出（不过要满足k的限制）
            while (k > 0 && !stack.isEmpty() && stack.peek() > c) {
                stack.pop();
                k--;
            }
            stack.push(c);
            i++;
        }
        
        // 上面的操作不一定会弹出要求的k个数字，需要把剩下的末尾的数弹出
        // 比如num=1234567，k=3
        while (k-- > 0) stack.pop();
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) sb.append(stack.pop());
        // 去除前置0
        // 当然有可能最终结果就是0，所以要留一位
        while (sb.length() > 1 && sb.charAt(sb.length() - 1) == '0') {
            sb.deleteCharAt(sb.length() - 1);
        }
        
        return sb.reverse().toString();
    }
}
```