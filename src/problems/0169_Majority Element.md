## 169. Majority Element (2020.10.04)

https://leetcode.com/problems/majority-element/

## Solution 1: Hash Table

- $TC:O(N)$
- $SC:O(N)$

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int num : nums) {
            int cnt = map.getOrDefault(num, 0) + 1;
            if (cnt > nums.length / 2) return num;
            map.put(num, cnt);
        }
        
        return -1;
    }
}
```

## Solution 2: Sorting

- $TC:O(NlogN)$
- $SC:O(1)$
- 因为majority出现的次数要超过N/2(即至少为N/2+1)，所以排序后在nums[N/2]的位置的数必定为majority。

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        
        Arrays.sort(nums);
        return nums[nums.length / 2];
    }
}
```

## Solution 3: Divide and Conquer

- $TC:O(NlogN)$, 很可能经常出现lm != rm的情况，就需要不断遍历数组
- $SC:O(logN)$
- 求当前数组的majority，就相当于求左半部分数组的majority和右半部分数组的majority。
- 如果lm和rm相等，说明左右两部分数组的majority是同一个数，那么当前数组的majority一定是lm/rm。
- 如果lm不等于rm，则要进一步遍历[l, r]来确定两者中哪一个才是当前数组的majority。
- 以此方式进行递归，直至l和r相等，说明只有一个元素，那么该元素就是这一部分的majority，直接返回即可。

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        return getMajority(nums, 0, nums.length - 1);
    }
    
    private int getMajority(int[] nums, int l, int r) {
        if (l == r) return nums[l];
        
        int mid = (r - l) / 2 + l;
        int lm = getMajority(nums, l, mid), rm = getMajority(nums, mid + 1, r);
        if (lm == rm) return lm;
        
        int cntLM = 0, cntRM = 0;
        for (int i = l; i < r + 1; i++) {
            if (nums[i] == lm) cntLM++;
            else if (nums[i] == rm) cntRM++;
        }
        
        return (cntLM > cntRM) ? lm : rm;
    }
}
```

## Solution 4: Randomization

- TC:由于其随机性，无法确定。但是因为majority的出现次数大于一半，所以随机抽取到的概率还是非常大的，因此在实践中反而比较快速
- $SC:O(1)$
- 随机抽取nums中的一个数，计算其出现的次数，如果大于nums.length / 2则找到majority。

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        
        Random random = new Random();
        
        while (true) {
            int num = nums[random.nextInt(nums.length)];
            int cnt = 0;
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] == num) cnt++;
            }
            
            if (cnt > nums.length / 2) return num;
        }
    }
}
```

## Solution 5: Moore Voting Algorithm

- $TC:O(N)$
- $SC:O(1)$
- 先假设nums[0]为当前的majority，开始遍历数组。
- 如果遇到的数等于当前的majority，则对计数器加1，否则减1.
- 当计数器被减到0的时候，说明这一部分不存在majority。
- 从下一个元素开始，重新设置当前的majority，重复以上过程，最终能保持cnt为正的majority即为所求。

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        
        int majority = nums[0], cnt = 0;
        for (int i = 0; i < nums.length; i++) {
            
            if (cnt == 0) majority = nums[i];
            
            if (nums[i] == majority) cnt++;
            else cnt--;
        }
        
        return majority;
    }
}
```

## Solution 6: Bit Manipulation

- $TC:O(N)$
- $SC:O(1)$
- 由于majority的出现次数超过数组的半数，我们可以根据这一特性来构建majority的每一位。
- 从binary的角度来看，在第i位上majority是0/1，那么最终遍历所有数的第i位后0/1的次数也会过半，反之亦然。

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        
        int offset = 1, majority = 0;
        for (int i = 0; i < 32; i++) {
            int cnt = 0;
            for (int num : nums) {
                // 此处注意使用()改变运算符优先级
                if ((num & offset) != 0) cnt++;
            }
            
            // cnt超过半数，说明这个位置应为1，直接将majority与offset进行或运算
            if (cnt > nums.length / 2) majority |= offset;
            offset <<= 1;
        }
        
        return majority;
    }
}
```

