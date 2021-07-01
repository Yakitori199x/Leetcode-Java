# 380. Insert Delete GetRandom O(1) (2021.05.07)

https://leetcode.com/problems/insert-delete-getrandom-o1/

## Similar Questions:
- 381. Insert Delete GetRandom O(1) - Duplicates allowed

```java
/**
 * Your RandomizedSet object will be instantiated and called as such:
 * RandomizedSet obj = new RandomizedSet();
 * boolean param_1 = obj.insert(val);
 * boolean param_2 = obj.remove(val);
 * int param_3 = obj.getRandom();
 */
```

## Solution 1: HashMap + List

- $TC:O(1) in average$, for each method
- $SC:O(n)$
- 首先，为了确保getRandom()能够以O(1)的效率被执行，我们肯定是需要一个List来维护当前保存在RandomizedSet中的元素，并提供random access。同时，我们通过Map来记录元素及其在List中的位置来保证insert和remove也能够以O(1)被执行。但是问题来了，如果只是不断insert加getRandom还好，但是一旦执行了remove且移除的不是List中的最后一个元素（最新加入RandomizedSet的元素），就要消耗O(n)的时间复杂度，不满足需求。
- 这时候我们发现，只要能保证每次remove的元素都在List末尾即可，要实现这个目的，很容易想到将对应元素和List的最后一个元素进行交换。因此，当我们要移除某个元素时，可以从Map中获取其index，将List的最后一个值last存储到该index，更新Map中last对应的位置，然后移除List最后一个元素且删除被移除元素在Map中的信息。

```java
class RandomizedSet {

    private Map<Integer, Integer> pos;
    private List<Integer> vals;
    private Random rand;
    
    /** Initialize your data structure here. */
    public RandomizedSet() {
        this.pos = new HashMap<>();
        this.vals = new ArrayList<>();
        this.rand = new Random();
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if (pos.containsKey(val)) return false;
        pos.put(val, vals.size());
        vals.add(val);
        return true;
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if (!pos.containsKey(val)) return false;
        int p = pos.get(val), last = vals.get(vals.size() - 1);
        vals.set(p, last);
        pos.put(last, p);
        // 单纯看remove(int index)的时间复杂度的话是O(n)
        // 但是我们确保了始终移除List的最末元素，那对应的时间复杂度就是O(1)
        vals.remove(vals.size() - 1);
        pos.remove(val);
        return true;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        return vals.get(rand.nextInt(vals.size()));
    }
}
```