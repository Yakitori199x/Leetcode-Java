# 1004. Max Consecutive Ones III (2021.05.01)

https://leetcode.com/problems/max-consecutive-ones-iii/

## Solution 1: Sliding window ver1

- $TC:O(n)$
- $SC:O(1)$
- 很直观的一种解法，维护一个滑动窗口，扩大窗口的右边界并记录窗口内应该被翻转的0的个数。
- 当窗口内0的个数大于k（k < 0)时，该窗口对应的子数组就不是一个合理的状态了。
- 这时，我们要缩短窗口的左边界，直至窗口内被翻转的0回到正常状态（k = 0)。

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int n = nums.length, i = 0, res = 0;
        for (int j = 0; j < n; j++) {
            if (nums[j] == 0) k--;
            while (k < 0) {
                if (nums[i] == 0) k++;
                i++;
            }
            // 每次更新窗口范围时都要实时获取窗口的大小来更新res
            res = Math.max(res, j - i + 1);
        }
        return res;
    }
}
```

## Solution 2: Sliding window ver2

- $TC:O(n)$
- $SC:O(1)$
- 该解法乍看上去不好理解，其实也是相当直观的。
- 相比于Solution1中一旦j扩张到不合理的状态，就开始右移i收缩窗口的范围，该解法是同时移动i和j，即扩张的同时也在收缩，保持窗口的大小不变。
- 为什么这样是可行的呢？我们可以看到，Solution1中被收缩的窗口必然不会是最终的res，因为它肯定是要比收缩前的合理状态来得小的。
- 根据这一点，Solution2保证了即使在不合理的状态，我们也保持窗口大小不变，相当于直接向右平移整个窗口。
- 同时，如果在移动过程中，窗口内的需要被翻转的0恢复到了正常状态，那么就有可能存在更大的满足条件的窗口。
- 这时候就停止移动i且继续扩张j，最终遍历结束后的窗口大小就是我们期望的最大窗口了。

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int n = nums.length, i = 0, j = 0;
        while (j < n) {
            if (nums[j] == 0) k--;
            if (k < 0) {
                if (nums[i++] == 0) k++;
            }
            j++;
        }
        return j - i;
    }
}
```