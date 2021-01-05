## 1375. Bulb Switcher III (2020.03.11)

https://leetcode.com/problems/bulb-switcher-iii/

## Solution 1

- $TC:O(N)$
- $SC:O(1)$
- 记录当前已开灯中的最大编号，并与开灯次数进行比较
- 如果两者相等则说明当前状态下所有已开灯均变蓝

```java
class Solution {
    public int numTimesAllBlue(int[] light) {
        int maxLightOn = 0;
        int res = 0;
        
        for (int i = 0; i < light.length; i++) {
            maxLightOn = Math.max(maxLightOn, light[i]);
            if (maxLightOn == i + 1) res++;
        }
        
        return res;
    }
}
```

## Solution 2

- $TC:O(N)$
- $SC:O(1)$
- 当所有开着的灯都变蓝时，开着的灯的编号之和会等于某个等差数列的和(1+2+3+...+n)

```java
class Solution {
    public int numTimesAllBlue(int[] light) {
        long sum = 0;
        int res = 0;
        
        for (int i = 0; i < light.length; i++) {
            sum += light[i];
            if (sum ==((long)(i + 1) * (i + 2) / 2)) res++; 
        }
        
        return res;
    }
}
```

