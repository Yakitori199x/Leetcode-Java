# 658. Find K Closest Elements (2021.04.14)

https://leetcode.com/problems/find-k-closest-elements/

## Solution 1: Two pointers

- $TC:O(n)$
- $SC:O(1)$
- 双指针解法，很直观。
- 维护l和r两个pointers来遍历arr，并确保在r-l=k-1（即l到r的范围内有k个数）的时候结束循环。
- 如果arr[r]和x的距离大于或等于arr[l]和x的距离，说明arr[r]必不可能是离x的k个最近的元素之一，排除其可能性后将r往左移动。
- 反之，则排除了arr[l]的可能性。最终的arr[l...r]就是我们要找的k个元素。

```java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int l = 0, r = arr.length - 1;
        while (r - l >= k) {
            if (arr[r] - x >= x - arr[l]) r--;
            else l++;
        }
        
        List<Integer> res = new ArrayList<>();
        for (int i = l; i <= r; i++) res.add(arr[i]);
        return res;
    }
}
```

## Solution 2: Binary Search ver1.

- $TC:O(k+logn)$
- $SC:O(1)$
- 先用二分搜索找到x在arr中的相对位置，然后设定l = lo - 1, r = lo两个指针向左右两侧扩展。
- 如果distL <= distR则当前arr[l]是一个合理的值，反之arr[r]是一个合理的值。
- 我们根据上述条件进行判断并移动l和r，直到发现count=k个数后停止。

```java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int n = arr.length, lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            if (arr[mid] < x) lo = mid + 1;
            else hi = mid;
        }
        
        int count = 0, l = lo - 1, r = lo;
        while (count < k) {
            int distL = (l >= 0) ? x - arr[l] : Integer.MAX_VALUE;
            int distR = (r < n) ? arr[r] - x : Integer.MAX_VALUE;
            if (distL <= distR) l--;
            else r++;
            count++;
        }
        
        List<Integer> res = new ArrayList<>();
        for (int i = l + 1; i < r; i++) res.add(arr[i]);
        return res;
    }
}
```

## Solution 3: Binary Search ver2.

- $TC:O(k+log(n-k))$
- $SC:O(1)$
- 这个版本的二分搜索方法相当地巧妙。
- 首先我们明确一个观点，我们要找的是arr中离x最近的k个数，且相同距离的情况下取index较小的数。
- 那么，我们实际上想找的就是一段合理的区间arr[i]~arr[i+k-1]（k个元素）。
- 对于这个i，我们可以使用二分搜索来查找，减小搜索空间的判断准则为(x - arr[mid])和(arr[mid+k] - x)的大小。
- 这是什么意思呢？其实，我们可以把arr[mid]到arr[mid+k]视为一个长度为k+1的滑动窗口。
- 如果x - arr[mid] > arr[mid+k] - x，我们再把窗口往左移，就肯定会引入一个距离更大的数（左端），且排除了一个距离较小的数。
- 因此这种情况下，我们只有把窗口向右移动才可能找到合适的解，即lo=mid+1。
- 反之，如果在x - arr[mid] <= arr[mid+k] - x的情况下，我们把窗口向右移动，就会排除掉左侧的一个较小数，引入了右侧的一个较大数。
- 所以在这种情况下，我们倾向于把窗口保持在当前位置来找到更合适的解，即hi=mid。
- 最终搜索结束时找到的lo就是我们要找的i，截取从i到i+k-1这一段，就是我们需要的k个元素了。

```java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        // 我们要找的i必不可能超过n-k，所以hi可以直接这么设置
        int n = arr.length, lo = 0, hi = n - k;
        while (lo < hi) {
            int mid = (hi - lo) / 2 + lo;
            // mid+k是一个开边界
            // 为什么不能用mid+k-1？比如[...3,3,4,7...], k = 3, x = 5
            if (x - arr[mid] > arr[mid + k] - x) lo = mid + 1;
            else hi = mid;
        }
        return Arrays.stream(arr, lo, lo + k).boxed().collect(Collectors.toList());
    }
}
```