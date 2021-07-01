# 496. Next Greater Element I (2021.05.07)

https://leetcode.com/problems/next-greater-element-i/

## Solution 1: Monotomic Stack + Map

- $TC:O(m + n)$
- $SC:O(m + n)$
- 维护一个单调递减栈来求出nums2中每个元素的next greater，并且将元素x及其对应的next greater保存在map里以便之后遍历nums1的时候获取结果。

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int[] res = new int[m];
        Deque<Integer> stack = new LinkedList<>();
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums2[i] > stack.peek()) {
                map.put(stack.pop(), nums2[i]);
            }
            stack.push(nums2[i]);
        }
        
        for (int i = 0; i < m; i++) res[i] = map.getOrDefault(nums1[i], -1);
        return res;
    }
}
```