# 904. Fruit Into Baskets (2021.05.08)

https://leetcode.com/problems/fruit-into-baskets/

## Solution 1: Sliding Window

- $TC:O(n)$
- $SC:O(n)$
- 本题实际上就是求仅包含两个不同数值元素的最长子数组，可以用滑动窗口求解。

```java
class Solution {
    public int totalFruit(int[] tree) {
        int n = tree.length, i = 0, j = 0;
        Map<Integer, Integer> counts = new HashMap<>();
        while (j < n) {
            counts.put(tree[j], counts.getOrDefault(tree[j], 0) + 1);
            if (counts.size() > 2) {
                counts.put(tree[i], counts.get(tree[i]) - 1);
                // 当key对应的value为指定值时移除
                // 相当于if (counts.get(tree[i]) == 0) counts.remove(tree[i]); i++;
                counts.remove(tree[i++], 0);
            }
            j++;
        }
        return j - i;
    }
}
```

## Solution 2: Sliding Window ver2

- $TC:O(n)$
- $SC:O(1)$
- 相比于Solution1节省了空间，因为始终保持map中只记录两个元素。

```java
class Solution {
    public int totalFruit(int[] tree) {
        int n = tree.length, res = 0, i = 0, j = 0;
        Map<Integer, Integer> counts = new HashMap<>();
        while (j < n) {
            counts.put(tree[j], counts.getOrDefault(tree[j], 0) + 1);
            while (counts.size() > 2) {
                counts.put(tree[i], counts.get(tree[i]) - 1);
                counts.remove(tree[i++], 0);
            }
            j++;
            res = Math.max(res, j - i);
        }
        return res;
    }
}
```

## Solution 3: Track lastest two types

- $TC:O(n)$
- $SC:O(1)$

```java
class Solution {
    public int totalFruit(int[] tree) {
        // 保持跟踪前两种水果以及上一种水果的数量
        int lastFruit = -1, secondLastFruit = -1, lastCount = 0, curMax = 0, res = 0;
        for (int fruit : tree) {
            // 如果当前水果和上一种或上上种一致，可以扩大最大数量（长度）
            // 否则，当前水果是一种全新的水果，最大数量就是上一种水果加上1
            if (fruit == lastFruit || fruit == secondLastFruit) curMax++;
            else curMax = lastCount + 1;
            
            if (fruit == lastFruit) {
                // 当前水果恰好是上一种水果，增加lastCount
                lastCount++;
            } else {
                // 当前水果不是上一种水果，那就只可能是全新的水果或是上上种水果
                // 那么我们需要重置lastCount为1（对应当前水果的数量），并且更新secondLastFruit和lastFruit
                lastCount = 1;
                secondLastFruit = lastFruit;
                lastFruit = fruit;
            }
            
            res = Math.max(res, curMax);
        }
        return res;
    }
}
```