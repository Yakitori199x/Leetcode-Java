## 349. Intersection of Two Arrays (2020.07.26)

https://leetcode.com/problems/intersection-of-two-arrays/

## Solution 1: Hashset

- $TC:O(m+n)$
- $SC:O(m)$
- Iterate an array and store elements to a hashset
- Iterate the other array and check if the element is contained in the set
- If it is, add it to intersection set

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> intersect = new HashSet<>();
        
        for (int num : nums1) set1.add(num);
        for (int num : nums2) {
            if (set1.contains(num)) intersect.add(num);
        }
        
        int[] res = new int[intersect.size()];
        int i = 0;
        for (int num : intersect) res[i++] = num;
        
        return res;
    }
}
```

## Solution 2: Sort two arrays, then apply two pointers

- $TC:O(nlogn)$, n is size of the larger array
- $SC:O(k)$, k is size of the intersection set

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> intersect = new HashSet<>();
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        
        int i = 0, j = 0;
        while (i < nums1.length && j < nums2.length) {    
            if (nums1[i] == nums2[j]) {
                intersect.add(nums1[i]);
                i++;
                j++;
            } else if (nums1[i] < nums2[j]) {
                i++;
            } else {
                j++;
            }
        }
        
        int[] res = new int[intersect.size()];
        int idx = 0;
        for (int num : intersect) res[idx++] = num;
        
        return res;
    }
}
```

## Solution 3

- $TC:O(nlogn)$, n is size of the larger array, can be limited to the smaller one by comparing the length before sorting
- $SC:O(k)$, k is size of the intersection set
- Sort one of the arrays, then iterate the other array to do binary search on sorted one

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> intersect = new HashSet<>();
        Arrays.sort(nums2);
        
        for (int i = 0; i < nums1.length; i++) {
            int left = 0, right = nums2.length - 1;
            while (left <= right) {
                int mid = (right - left) / 2 + left;
                if (nums2[mid] == nums1[i]) {
                    intersect.add(nums1[i]);
                    break;
                } else if (nums2[mid] < nums1[i]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        
        int[] res = new int[intersect.size()];
        int idx = 0;
        for (int num : intersect) res[idx++] = num;
        
        return res;
    }
}
```

