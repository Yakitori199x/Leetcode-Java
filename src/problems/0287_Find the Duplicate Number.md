# 287. Find the Duplicate Number (2021.01.17)

https://leetcode.com/problems/find-the-duplicate-number/

## Solution 1: Set

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int findDuplicate(int[] nums) {
        if (nums == null) return -1;
        
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) return num;
            set.add(num);
        }
        
        return -1;
    }
}
```

## Solution 2: Negate values

- $TC:O(n)$
- $SC:O(1)$
- 类似Leetcode.448的解法，但会改变原数组的信息。

```java
class Solution {
    public int findDuplicate(int[] nums) {
        if (nums == null) return -1;
        
        for (int i = 0; i < nums.length; i++) {
            // 因为length为n+1且values均在[1,n]，所以不必担心越界
            int idx = Math.abs(nums[i]);
            if (nums[idx] > 0) nums[idx] = -nums[idx];
            else return idx;
        }
        
        return -1;
    }
}
```

## Solution 3: Binary Search

- $TC:O(nlogn)$
- $SC:O(1)$
- 基于二分搜索的思想设计出的解法。我们的目的就是在[1,n]中找到重复的值，那么我们可以先对中间的值mid进行判断。
- 判断的方式是统计nums中小于等于mid的值的个数，如果在这个范围内没有重复的值，个数应该是不超过mid的，则重复的值应该在[mid+1,n]。
- 否则说明重复的值存在于[1,mid]。每次可以缩小一半的搜索范围，直到只剩下一个可搜索的值。
- p.s. 该方法只适用于仅存在一个重复的值的情况

```java
class Solution {
    public int findDuplicate(int[] nums) {
        if (nums == null) return -1;
        
        int n = nums.length - 1;
        int lt = 1, rt = n;
        while (lt < rt) {
            int mid = lt + (rt - lt) / 2;
            int count = 0;
            
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] <= mid) count++;
            }
            
            if (count > mid) rt = mid;
            else lt = mid + 1;
        }
        
        return lt;
    }
}
```

## Solution 4: CyFloyd's Tortoise and Hare (Cycle Detection)

- $TC:O(n)$
- $SC:O(1)$
- 从0开始以每个位置的值num[i]作为index去获取下一个值nums[nums[i]]，可以形成一个链表。如果nums中有重复的值，这个链表就会存在cycle，且cycle的entrypoint就是重复的值。
- 详细分析见：https://leetcode.com/problems/find-the-duplicate-number/discuss/72846/My-easy-understood-solution-with-O(n)-time-and-O(1)-space-without-modifying-the-array.-With-clear-explanation (lf963的comment)

```java
class Solution {
    public int findDuplicate(int[] nums) {
        if (nums == null) return -1;
        
        int slow = nums[0], fast = nums[nums[0]];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        
        // 因为第一次遍历时fast比slow往前一步开始走，所以相遇时满足的是(x-1) + y + z = 2 * (x + y)
        // 即z = x + 1，所以第二次遍历需要从0开始走
        fast = 0;
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        
        return fast;
    }
}
```