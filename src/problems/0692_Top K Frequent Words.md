# 692. Top K Frequent Words (2021.04.22)

https://leetcode.com/problems/top-k-frequent-words/

## Solution 1: Sorting

- $TC:O(nlogn)$
- $SC:O(n)$
- 先把words中每个单词及其对应出现频率存储到map中，然后将map的entries组织成一个list进行排序。
- 排序的基准是出现频率高的entry在前，出现频率相等的情况下，字典序较小的entry排在前面。

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freq = new HashMap<>();
        for (String word : words) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        List<Map.Entry<String, Integer>> entries = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : freq.entrySet()) entries.add(entry);
        Collections.sort(entries, (a, b) -> a.getValue() == b.getValue() ? 
                         a.getKey().compareTo(b.getKey()) : b.getValue() - a.getValue());
        
        List<String> res = new ArrayList<>();
        for (int i = 0; i < k; i++) res.add(entries.get(i).getKey());
        return res;
    }
}
```

## Solution 2: Map + Priority Queue (Min Heap)

- $TC:O(nlogk)$
- $SC:O(n)$
- 同样是构建一个word到出现频率的map，但这个解法使用最小堆优先队列来保存top-k words。

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freq = new HashMap<>();
        for (String word : words) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        // 由于是最小堆，所以频率低word的要保存在队首
        Queue<Map.Entry<String, Integer>> pq = new PriorityQueue<>(
            (a, b) -> a.getValue() == b.getValue() ? 
            b.getKey().compareTo(a.getKey()) : a.getValue() - b.getValue()
        );
        
        for (Map.Entry<String, Integer> entry : freq.entrySet()) {
            pq.offer(entry);
            // 始终只保存当前状态下的top-k words
            if (pq.size() > k) pq.poll();
        }
        
        Deque<String> res = new LinkedList<>();
        // 因为pq中弹出的顺序刚好是反过来的，直接向ArrayList用头插法在k很大的情况下可能会使复杂度变成O(n^2)
        while (!pq.isEmpty()) res.addFirst(pq.poll().getKey());
        return new ArrayList<>(res);
    }
}
```

## Solution 3: Map + Bucket Sort + Trie

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    class TrieNode {
        public TrieNode[] children = new TrieNode[26];
        public String word = null;
    }
    
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freq = new HashMap<>();
        for (String word : words) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        TrieNode[] buckets = new TrieNode[words.length + 1];
        for (String word : freq.keySet()) {
            int frequency = freq.get(word);
            if (buckets[frequency] == null) buckets[frequency] = new TrieNode();
            addWord(buckets[frequency], word);
        }
        
        List<String> res = new ArrayList<>();
        for (int i = words.length; i > 0 && res.size() < k; i--) {
            if (buckets[i] == null) continue;
            getWords(buckets[i], res, k);
        }
        return res;
    }
    
    private void addWord(TrieNode node, String word) {
        for (char c : word.toCharArray()) {
            if (node.children[c - 'a'] == null) node.children[c - 'a'] = new TrieNode();
            node = node.children[c - 'a'];
        }
        node.word = word;
    }
    
    private void getWords(TrieNode node, List<String> res, int k) {
        if (res.size() == k) return ;
        if (node.word != null) res.add(node.word);
        
        for (int i = 0; i < 26; i++) {
            if (node.children[i] == null) continue;
            getWords(node.children[i], res, k);
        }
    }
}
```