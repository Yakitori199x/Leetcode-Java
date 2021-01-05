## 350. Intersection of Two Arrays II (2020.07.26)

https://leetcode.com/problems/intersection-of-two-arrays-ii/

## Solution 1: HashMap

- $TC:O(m+n)$, O(m) for creating hashmap, O(n) for iterating the second array
- $SC:O(m)$, O(m) for storing elements into hashmap
- To record the number of each elements in an array

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        List<Integer> intersect = new LinkedList<>();
        
        for (int i = 0; i < nums1.length; i++) {
            if (map.containsKey(nums1[i])) map.put(nums1[i], map.get(nums1[i]) + 1);
            else map.put(nums1[i], 1);
        }
        
        for (int i = 0; i < nums2.length; i++) {
            if (map.containsKey(nums2[i]) && map.get(nums2[i]) > 0) {
                intersect.add(nums2[i]);
                map.put(nums2[i], map.get(nums2[i]) - 1);
            }
        }
        
        int[] res = new int[intersect.size()];
        int idx = 0;
        for (int num : intersect) res[idx++] = num;
        
        return res;
    }
}
```

## Solution 2

- $TC:O(max(m,n))$, iterate two arrays simultaneously until either one ends
- $SC:O(k)$, k is size of the intersection set
- If the arrays are sorted, we can use two pointers

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        List<Integer> intersect = new LinkedList<>();
        
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

