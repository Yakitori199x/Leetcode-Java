# 347. Top K Frequent Elements (2021.01.27)

https://leetcode.com/problems/top-k-frequent-elements/


## Solution 1: Bucket Sort

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        if (nums == null) return null;
        
        // 创建<num, freq>的map，统计每个值出现的次数
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        // 初始化bucket
        List<List<Integer>> buckets = new ArrayList<>();
        for (int i = 0; i <= nums.length; i++) {
            buckets.add(new ArrayList<>());
        }
        
        // 根据每个num的频率，将num放置到以freq为索引的bucket中
        // 这样buckets就表示不同freq下的值的集合，有点逆向map的感觉
        for (int num : freqMap.keySet()) {
            int freq = freqMap.get(num);
            buckets.get(freq).add(num);
        }
        
        // 从最大频率对应的nums开始找，直至发现k个后停止
        int[] res = new int[k];
        for (int i = nums.length; i > 0; i--) {
            List<Integer> candidates = buckets.get(i);
            for (int j = 0; j < candidates.size() && k > 0; j++) {
                res[--k] = candidates.get(j);
            }
        }
        
        return res;
    }
}
```

## Solution 2: Max heap

- $TC:O(n)$
- $SC:O(n)$
- 也可以用min heap做

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        if (nums == null) return null;
        
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }
        
        // lambda实现了Comparator的compare方法
        // 因为这里想要的是最大堆，而PriorityQueue默认是把判断后顺序小的值放在前面，所以要反过来
        PriorityQueue<Map.Entry<Integer, Integer>> pq = new PriorityQueue<>((a, b) -> b.getValue() - a.getValue());
        for (Map.Entry<Integer, Integer> e : freqMap.entrySet()) {
            pq.offer(e);
        }
        
        int[] res = new int[k];
        for (int i = 0; i < k; i++) {
            res[i] = pq.poll().getKey();
        }
        
        return res;
    }
}
```

## Solution 3: Quick Select
思路：先构造出num和freq的Map，然后构造一个由所有unique nums组成的list（map.keySet()）。之后对这个list进行partition，不过partition的比较标准不是list里的值本身，而是其对应的freq，这样就可以获得freq最高的k个数。

TBA

https://leetcode.com/problems/top-k-frequent-elements/solution/

## Solution 4: TreeMap

TBA

https://leetcode.com/problems/top-k-frequent-elements/discuss/81635/3-Java-Solution-using-Array-MaxHeap-TreeMap