# 381. Insert Delete GetRandom O(1) - Duplicates allowed (2021.05.07)

https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed/

## Similar Questions:
- 380. Insert Delete GetRandom O(1)

```java
/**
 * Your RandomizedCollection object will be instantiated and called as such:
 * RandomizedCollection obj = new RandomizedCollection();
 * boolean param_1 = obj.insert(val);
 * boolean param_2 = obj.remove(val);
 * int param_3 = obj.getRandom();
 */
```

## Solution 1: HashMap + LinkedHashSet

- $TC:O(1) in average$, for each method
- $SC:O(n)$
- 和LC380类似，不过这一题RandomizedCollection中是会出现duplicates的。因此，我们可以扩展LC380中的Map，让其成记录val及该值在List中所有出现位置的index，即int到Set/List的Map。在该解法中，我们采用的是基于Set的实现，如果是基于List的实现则需要额外维护一些信息，可参考Solution2。

```java
class RandomizedCollection {

    private Map<Integer, Set<Integer>> pos;
    private List<Integer> vals;
    private Random rand;
    
    /** Initialize your data structure here. */
    public RandomizedCollection() {
        this.pos = new HashMap<>();
        this.vals = new ArrayList<>();
        this.rand = new Random();
    }
    
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    public boolean insert(int val) {
        boolean contains = true;
        if (!pos.containsKey(val)) {
            // 这里用的Set的具体实现是LinkedHashSet
            // 因为在remove的时候我们要获取任意一个val的位置，需要用到iterator()
            // 如果是一般的HashSet实现，返回iterator需要消耗O(n)
            // 而LinkedHashSet实现已经建立好了元素之间的reference，返回iterator需要消耗O(1)
            pos.put(val, new LinkedHashSet<>());
            contains = false;
        }
        pos.get(val).add(vals.size());
        vals.add(val);
        return !contains;
    }
    
    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    public boolean remove(int val) {
        if (!pos.containsKey(val)) return false;
        // 获取val在vals中的任意一个位置的index，不影响结果
        int valIdx = pos.get(val).iterator().next(), lastIdx = vals.size() - 1, last = vals.get(lastIdx);
        // 一定要先将valIdx从pos中移除，否则遇到val和last相等，且valIdx小于lastIdx的情况时，移除一个val会把Set里面的两个index均移除（正确的应该是移除lastIdx，保留valIdx）
        // 同时vals中只有一个val被移除，而剩余的val很可能一直不被移除（比如Set空了，要移除这个val就直接返回false），在之后求random的时候就会影响结果
        pos.get(val).remove(valIdx);
        if (valIdx < lastIdx) {
            vals.set(valIdx, last);
            pos.get(last).remove(lastIdx);
            pos.get(last).add(valIdx);
        }
        vals.remove(lastIdx);
        if (pos.get(val).size() == 0) pos.remove(val);
        return true;
    }
    
    /** Get a random element from the collection. */
    public int getRandom() {
        return vals.get(rand.nextInt(vals.size()));
    }
}
```

## Solution 2: HashMap + List

- $TC:O(1) in average$, for each method
- $SC:O(n)$
- 如果只是简单地按照Solution1的实现把Set改为List，且remove的时候始终选择val对应的List中最后一个index，就会出问题。
- 比如对某个val来说，有pos[val] = [..., 5, 9]。
- 当执行remove去移除另外某个值且val恰好是vals的最后一个元素时，如果被移除的值对应在vals的index比5还来得小，pos[val]可能被修改为[..., 5, 2]。
- 在这样的情况下，如果要移除val且vals的最后一个元素也是val，就会造成pos[val] = [..., 5]，即index=2的val的位置信息被删除了，实际上应该被删除的是index=5的位置。
- 这就说明我们不能一味地去修改last对应的List的最后一个index，而是需要记录下vals中的每个值对应的index被记录在pos[val]的哪一个位置，修改相应的位置即可。

```java
class RandomizedCollection {

    private Map<Integer, List<Integer>> pos;
    private List<int[]> vals;
    private Random rand;
    
    /** Initialize your data structure here. */
    public RandomizedCollection() {
        this.pos = new HashMap<>();
        this.vals = new ArrayList<>();
        this.rand = new Random();
    }
    
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    public boolean insert(int val) {
        boolean contain = pos.containsKey(val);
        if (!contain) pos.put(val, new ArrayList<>());
        pos.get(val).add(vals.size());
        // 将val加入vals时，不仅要添加值本身，还要记录当前val的位置被记录在pos[val]的哪个位置
        vals.add(new int[]{val, pos.get(val).size() - 1});
        return !contain;
    }
    
    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    public boolean remove(int val) {
        if (!pos.containsKey(val)) return false;
        
        List<Integer> valPos = pos.get(val);
        int valLastIdx = valPos.get(valPos.size() - 1), lastIdx = vals.size() - 1;
        int[] last = vals.get(lastIdx);
        
        // 将last放到待删除的val的位置
        vals.set(valLastIdx, last);
        // 因为last的位置改变，修改last[0]在pos中对应的位置
        pos.get(last[0]).set(last[1], valLastIdx);
        
        vals.remove(lastIdx);
        valPos.remove(valPos.size() - 1);
        if (valPos.size() == 0) pos.remove(val);
        return true;
    }
    
    /** Get a random element from the collection. */
    public int getRandom() {
        return vals.get(rand.nextInt(vals.size()))[0];
    }
}
```