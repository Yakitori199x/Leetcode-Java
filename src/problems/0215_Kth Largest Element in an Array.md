# 215. Kth Largest Element in an Array (2021.01.23)

https://leetcode.com/problems/kth-largest-element-in-an-array/

## Solution 1: Min heap (Priority queue)

- $TC:O(nlogk)$
- $SC:O(k)$
- 和[703. Kth Largest Element in a Stream]是一样的思路

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length < k) return -1;
        
        Queue<Integer> pq = new PriorityQueue<>();
        for (int num : nums) {
            pq.offer(num);
            if (pq.size() > k) {
                pq.poll();
            }
        }
        
        return pq.peek();
    }
}
```
## Solution 2: Quick Select (Partition)

- $TC:O(n)$ in best case, $O(n^2)$ in worst case
- $SC:O(1)$
- 使用partition算法，每次partition可以确定pivot在nums中所处的位置，与我们要求的kth判断即可。
- pivot的选择会影响到算法效率，刚好对半切分时，有T(n) = T(n/2) + O(n)，即TC为O(n)。如果切分的位置非常在两侧，则为T(n) = T(n - 1) + O(n)，即TC为O(n^2)。
- 通过在最初的partition前对nums进行shuffle可以极大地避免O(n^2)的情况。

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length < k) return -1;
        
        // 这里把kth largest转换成对应的index，可以简化对target的操作（不用考虑k-pos的情况）
        // 也可以保持k，但是partition变为左侧>=pivot，右侧<pivot
        int idx = nums.length - k;
        int i = 0, j = nums.length - 1, res = 0;
        shuffle(nums);
        
        while (i <= j) {
            int pos = partition(nums, i, j);
            if (pos == idx) {
                res = nums[pos];
                break;
            }
            else if (pos < idx) i = pos + 1;
            else j = pos - 1;
        }
        
        return res;
    }
    
    private void shuffle(int[] nums) {
        for (int i = 1; i < nums.length; i++) {
            Random random = new Random();
            int p = random.nextInt(i);
            swap(nums, p, i);
        }
    }
    
    // Lomuto partition
    // 如果nums里有很多相等的元素，会使partition的位置变差
    private int partition(int[] nums, int i, int j) {
        int pivot = nums[j];
        for (int cur = i; cur < j; cur++) {
            if (nums[cur] < pivot) {
                swap(nums, i++, cur);
            }
        }
        
        swap(nums, i, j);
        return i;
    }

    // Hoare partition
    private int partition(int[] nums, int i, int j) {
        int pivot = nums[i], l = i + 1, r = j;
        
        while (l <= r) {
            if (nums[l] > pivot && nums[r] < pivot) {
                swap(nums, l++, r--);
            }
            // 这种实现使得即使nums中有很多相等的元素，也可以使partition的位置靠近中心
            if (nums[l] <= pivot) l++;
            if (nums[r] >= pivot) r--;
        }
        
        swap(nums, i, r);
        return r;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```



## Solution 3: Heap sort

- $TC:O(nlogn)$
- $SC:O(n)$
- Heap sort解法和优先队列的实现类似，都是基于heap。
- Solution 1是直接使用了Java标准库的PriorityQueue，这里是自己实现了一个最大堆。

```java
class Solution {
    // 需要维护一个全局的size而不是直接使用nums.length
    // 否则在弹出最大元素仍会基于nums.length进行heapify
    private int heapSize;
    
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length < k) return -1;
        
        // 先基于nums建立一个heap，然后不断弹出最大元素并重新堆化，直到kth largest处于nums[0]
        buildMaxHeap(nums);
        for (int i = 0; i < k - 1; i++) {
            // 直接对heapSize递减，利用heapSize索引最后一个元素的同时维护了正确的heapSize
            nums[0] = nums[--heapSize];
            maxHeapify(nums, 0);
        }
        
        return nums[0];
    }
    
    private void buildMaxHeap(int[] nums) {
        heapSize = nums.length;
        // 堆的最后一个结点对应的父结点为[heapSize / 2 - 1]
        // 也就是说从底向上，略过所有叶子结点，从第一个有叶子结点的子树开始堆化
        for (int i = heapSize / 2 - 1; i >=0; i--) {
            maxHeapify(nums, i);
        }
    }
    
    // 这里使用数组来实现平衡二叉树，左右子结点的index可以直接计算获得
    private int left(int i) {
        return i * 2 + 1;
    }
    
    private int right(int i) {
        return i * 2 + 2;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[j];
        nums[j] = nums[i];
        nums[i] = temp;
    }
    
    private void maxHeapify(int[] nums, int i) {
        int largest = i;
        int l = left(i), r = right(i);
        
        // 注意这里需要跟nums[largest]比较，选出root，left和right中最大的结点
        if (l < heapSize && nums[l] > nums[largest]) {
            largest = l;
        }
        
        if (r < heapSize && nums[r] > nums[largest]) {
            largest = r;
        }
        
        if (largest != i) {
            swap(nums, i, largest);
            // 可能从root换下来的结点并不满足heap的要求（根结点比其所有的子结点都大）
            // 需要对以其为root的子树再进行heapify
            maxHeapify(nums, largest);
        }
    }
}
```