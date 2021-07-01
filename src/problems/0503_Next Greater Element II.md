# 503. Next Greater Element II (2021.03.22)

https://leetcode.com/problems/next-greater-element-ii/

## Solution 1: Monotonous Stack + Circular Iteration

- $TC:O(n)$
- $SC:O(n)$
- 维护一个单调递减栈即可找到每个数的下一个大于其的数。
- 由于本题的nums是一个循环数组（即末尾元素的next为nums的首个元素），我们可以通过遍历nums两次来实现。
- 而遍历nums两次不需要写两个for，也不需要把nums复制一份和原本的连接起来。
- 只需要用一个i从0遍历到2n即可，期间注意用mod来控制i所指向的位置。

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] res = new int[nums.length];
        Arrays.fill(res, -1);
        Deque<Integer> stack = new LinkedList<>();
        
        for (int i = 0; i < 2 * n; i++) {
            while (!stack.isEmpty() && nums[i % n] > nums[stack.peek()]) {
                res[stack.pop()] = nums[i % n];
            }
            stack.push(i % n);
        }
        
        return res;
    }
}
```