# 967. Numbers With Same Consecutive Differences (2021.03.05)

https://leetcode.com/problems/numbers-with-same-consecutive-differences/

## Solution 1: DFS

- $TC:O(10*2^n)$
- $SC:O(n)$
- 以1~9为第一位数字进行DFS，且下一位数字肯定是(num % 10 + k)和(num % 10 - k)。
- 我们只需要检查一下下一位candidate是否在合理的范围内，然后和当前的num构成新的值递归下去即可。
- 当数值的所有的n位都构造完成后，即可加入最终的list。

```java
class Solution {
    public int[] numsSameConsecDiff(int n, int k) {
        List<Integer> res = new ArrayList<>();
        for (int i = 1; i <= 9; i++) {
            // 由于i本身就形成了第一位，所以传入的是n-1
            numsSameConsecDiff(n - 1, k, i, res);
        }
        return res.stream().mapToInt(Integer::valueOf).toArray();
    }
    
    private void numsSameConsecDiff(int n, int k, int num, List<Integer> res) {
        if (n == 0) {
            res.add(num);
            return;
        }
        
        int lastDigit = num % 10;
        // 如果下一位的可能取值大于等于10，说明不存在合理的解
        if (lastDigit + k < 10) {
            numsSameConsecDiff(n - 1, k, num * 10 + lastDigit + k, res);
        }
        
        // 如果下一位的可能取值小于0，说明不存在合理的解
        // 同时k!=0的加入是为了防止重复构造k为0的情况（即lastDigit+k与lastDigit-k是相同值）
        // 比如k=0时，上面if中的调用可能会最终生成11，而在这个if中的调用同样是生成11
        if (k != 0 && lastDigit - k >= 0) {
            numsSameConsecDiff(n - 1, k, num * 10 + lastDigit - k, res);
        }
    }
}
```

## Solution 2: BFS

- $TC:O(10*2^n)$
- $SC:O(n)$

```java
class Solution {
    public int[] numsSameConsecDiff(int n, int k) {
        List<Integer> res = new ArrayList<>();
        Queue<Integer> queue = new LinkedList<>();
        
        for (int i = 1; i <= 9; i++) {
            int count = 1;
            queue.add(i);
            
            while (!queue.isEmpty()) {
                int size = queue.size();
                for (int j = 0; j < size; j++) {
                    int num = queue.poll();
                    if (count == n) {
                        res.add(num);
                        continue;
                    }
                    
                    int lastDigit = num % 10;
                    if (lastDigit + k < 10) queue.add(num * 10 + lastDigit + k);
                    if (k != 0 && lastDigit - k >= 0) queue.add(num * 10 + lastDigit - k);
                }
                count++;
            }
        }
        
        return res.stream().mapToInt(Integer::valueOf).toArray();
    }
}
```