# 962. Maximum Width Ramp (2021.03.19)

https://leetcode.com/problems/maximum-width-ramp/

## Solution 1: Monotonous Stack

- $TC:O(n)$
- $SC:O(n)$
- Naive的思路就是遍历所有的(i,j)以求得最宽的ramp，时间复杂度为O(n^2)。
- 但是，对于某个i来说，如果存在j>i满足A[j] >= A[i]，那么对于i之后的大于A[i]的其他元素便无需判断。
- 因此，我们可以遍历A以创建一个单调递减的栈，这样就缩小了搜索的范围。
- 在这个基础上，我们可以发现，比起从左到右移动j，从右到左遍历更有优势，只要找到A[j] >= A[i]，那么必定是当前i的最优解。
- 其次，利用单调递减栈的性质，在A[j] < A[i]的情况下，由于栈内元素大于A[i]，必定与A[j]构不成ramp。
- 也就是说，对栈内的剩余元素来说，其与A[j]的关系也不用再遍历了。

```java
class Solution {
    public int maxWidthRamp(int[] A) {
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < A.length; i++) {
            if (stack.isEmpty() || A[stack.peek()] > A[i]) {
                stack.push(i);
            }
        }
        
        int res = 0;
        for (int j = A.length - 1; j >= 0; j--) {
            while (!stack.isEmpty() && A[j] >= A[stack.peek()]) {
                res = Math.max(res, j - stack.pop());
            }
        }
        return res;
    }
}
```

## Solution 2: Binary Search

- $TC:O(nlogn)$
- $SC:O(n)$
- 遍历A构造单调递减栈的同时，用二分搜索查看当前元素在最小栈中所处的位置，或者说找到小于或等于当前元素的第一个元素。
- 因此这里不需要出栈操作，我们可以直接用一个List来模拟单调递减栈。
- 如果当前元素小于栈顶元素，则直接加入List。否则，进行二分搜索找到其应该处于的位置，计算出width（该情况下已经确保了当前元素大于等于栈顶元素）。

```java
class Solution {
    public int maxWidthRamp(int[] A) {
        List<Integer> monoStk = new ArrayList<>();
        int res = 0;
        for (int i = 0; i < A.length; i++) {
            if (monoStk.isEmpty() || A[monoStk.get(monoStk.size() - 1)] > A[i]) {
                monoStk.add(i);
            } else {
                int l = 0, r = monoStk.size() - 1;
                while (l <= r) {
                    int mid = (r - l) / 2 + l;
                    if (A[i] >= A[monoStk.get(mid)]) r = mid - 1;
                    else l = mid + 1;
                }
                res = Math.max(res, i - monoStk.get(l));
            }
        }
        return res;
    }
}
```