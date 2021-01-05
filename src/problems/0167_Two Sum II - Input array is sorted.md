## 167. Two Sum II - Input array is sorted (2020.07.07)

https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/

## Solution 1: Two pointers

- $TC:O(N)$
- $SC:O(1)$

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] res = new int[2];
        int i = 0, j = numbers.length - 1;
        
        while (i < j) {
            int sum = numbers[i] + numbers[j];
            if (sum == target) {
                res[0] = i + 1;
                res[1] = j + 1;
                return res;
            } else if (sum > target) {
                j--;
            } else {
                i++;
            }
        }
        
        return res;
    }
}
```

## Solution 2: Iteration + Binary Search

- $TC:O(NlogN)$
- $SC:O(1)$

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] res = new int[2];
        
        for (int i = 0; i < numbers.length - 1; i++) {
            int j = i + 1;
            int k = numbers.length - 1;
            while (j <= k) {
                int mid = (k - j) / 2 + j;
                if (numbers[mid] == target - numbers[i]) {
                    res[0] = i + 1;
                    res[1] = mid + 1;
                    return res;
                } else if (numbers[mid] < target - numbers[i]) {
                    j = mid + 1;
                } else {
                    k = mid - 1;
                }
            }
        }
        return res;
    }
}
```

