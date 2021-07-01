# 605. Can Place Flowers (2021.04.12)

https://leetcode.com/problems/can-place-flowers/

## Solution 1: Greedy

- $TC:O(m)$
- $SC:O(1)$
- 该解法就是遍历的同时找到还未被种植的位置i（flowerbed[i] = 0），然后检查其左侧位置（prev）和右侧位置（next）是否已经被种植。
- 若没有，说明i处是一个可以种花的位置（对数组的两端要进行特殊处理），累加count并将flowerbed[i]设置为1。

```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int m = flowerbed.length, count = 0;
        for (int i = 0; i < m && count < n; i++) {
            if (flowerbed[i] == 0) {
                int prev = (i == 0) ? 0 : flowerbed[i - 1];
                int next = (i == m - 1) ? 0 : flowerbed[i + 1];
                if (prev == 0 && next == 0) {
                    flowerbed[i] = 1;
                    count++;
                }
            }
        }
        return count == n;
    }
}
```

## Solution 2: Greedy ver2

- $TC:O(m)$
- $SC:O(1)$
- Solution1需要修改原数组flowerbed，并不是一个理想的解法。我们可以通过直接修改i来保持flowerbed不变。
- 当我们确认i处可以种花，或是确认i处已经被种花时，我们都知道i+1处肯定不是一个合理的位置，因此我们可以直接对i累加2，跳过i+1。
- 当然，如果位置i处虽然没有种花，但其两侧prev或next已经有花了，那我们就只对i累加1。

```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int m = flowerbed.length, count = 0, i = 0;
        while (i < m && count < n) {
            if (flowerbed[i] == 0) {
                int prev = (i == 0) ? 0 : flowerbed[i - 1];
                int next = (i == m - 1) ? 0 : flowerbed[i + 1];
                if (prev == 0 && next == 0) {
                    i += 2;
                    count++;
                } else {
                    i++;
                }
            } else {
                i += 2;
            }
        }
        return count == n;
    }
}
```

## Solution 3: Divide and Count

- $TC:O(m)$
- $SC:O(1)$
- 遍历flowerbed并计算出每一段连续的0的长度count，如果是数组两端的情况，对应能够种下的花为count / 2。
- 如果是数组中间被两个1夹着的情况，对应能够种下的花为(count - 1) / 2。
- 这个实现中比较巧妙的是将count起始值设定为1，把左端的情况和中间的情况合并起来，简化了实现。

```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int m = flowerbed.length, count = 1;
        for (int i = 0; i < m; i++) {
            if (flowerbed[i] == 0) {
                count++;
            } else {
                n -= (count - 1) / 2;
                count = 0;
            }
        }
        
        if (count > 0) n -= count / 2;
        return n <= 0;
    }
}
```