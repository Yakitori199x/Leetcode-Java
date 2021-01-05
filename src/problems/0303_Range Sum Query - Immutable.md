## 303. Range Sum Query - Immutable (2020.11.10)

https://leetcode.com/problems/range-sum-query-immutable/


```java
/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(i,j);
 */
```

## Solution 1: Brute force

- $TC:O(1)$ for construction, $O(N)$ for sumRange
- $SC:O(1)$

```java
class NumArray {

    private int[] nums;
    
    public NumArray(int[] nums) {
        this.nums = nums;
    }
    
    public int sumRange(int i, int j) {
        int res = 0;
        for (int idx = i; idx <= j; idx++) {
            res += nums[idx];
        }
        return res;
    }
}
```

## Solution 2: Optimize sumRange with 2-D array

- $TC:O(N^2)$ for construction, $O(1)$ for sumRange
- $SC:O(N^2)$, MLE
- Space complexity can be further optimized by taking half of sum[][] into a Map.

```java
class NumArray {
    
    private int[][] sum;
    
    public NumArray(int[] nums) {
        int n = nums.length;
        sum = new int[n][n];
        for (int i = 0; i < n; i++) {
            sum[i][i] = nums[i];
            for (int j = i + 1; j < n; j++) {
                sum[i][j] = sum[i][j - 1] + nums[j];
            }
        }
    }
    
    public int sumRange(int i, int j) {
        return sum[i][j];
    }
}

```

## Solution 3: Optimize sumRange with 1-D array

- $TC:O(N)$ for construction, $O(1)$ for sumRange
- $SC:O(N)$

```java
class NumArray {
    
    private int[] sums;

    public NumArray(int[] nums) {
        sums = new int[nums.length + 1];
        for (int i = 1; i <= nums.length; i++) {
            // sums[i] = nums[0] + nums[1] + ... + nums[i-1]
            // res = nums[i] + ... + nums[j] = sums[j+1] - sums[i]
            sums[i] = sums[i - 1] + nums[i - 1];
        }
    }
    
    public int sumRange(int i, int j) {
        return sums[j + 1] - sums[i];
    }
}
```

