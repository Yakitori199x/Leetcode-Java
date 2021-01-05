## 11. Container With Most Water (2020.05.19)

https://leetcode.com/problems/container-with-most-water/

## Solution 1: Brute force -- Search all the cases (all possible pairs of lines)

- $TC:O(N^2)$
- $SC:O(1)$

```java
class Solution {
    public int maxArea(int[] height) {
        int res = 0;
        for (int i = 0; i < height.length; i++) {
            for (int j = i + 1; j < height.length; j++) {
                res = Math.max(res, Math.min(height[i], height[j]) * (j - i));
            }
        }
        return res;
    }
}
```

## Solution 2: Two pointers

- $TC:O(N)$
- $SC:O(1)$
- Because the area is limited by the shorter line, we can move the pointer points to the shorter line to decrease search cases, as moving the longer one only gets smaller area.
- Some clarification and proof:
   - https://leetcode.com/problems/container-with-most-water/discuss/6099/yet-another-way-to-see-what-happens-in-the-on-algorithm
   - https://leetcode.com/problems/container-with-most-water/discuss/6089/Anyone-who-has-a-O(N)-algorithm/7268

```java
class Solution {
    public int maxArea(int[] height) {
        int res = 0;
        int left = 0, right = height.length - 1;
        
        while (left < right) {
            res = Math.max(res, Math.min(height[left], height[right]) * (right - left));
            if (height[left] < height[right]) left++;
            else right--;
        }
        
        return res;
    }
}
```

