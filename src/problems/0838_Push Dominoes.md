# 838. Push Dominoes (2021.06.02)

https://leetcode.com/problems/push-dominoes/

## Solution 1: Two pointers

- $TC:O(n)$
- $SC:O(n)$
- 不难发现，`.`的位置的最终状态其实是由其两侧离该点最近的`L`和`R`决定的。
- 因此，我们可以将一段连续的`.`视为一个Group。如果满足`R...R`或是`L...L`，那么Group对应范围内的点的最终状态必然是`R`或者`L`。
- 如果满足`L...R`，则范围内的点不会受到两侧骨牌的影响，最终状态仍然为`.`。
- 如果满足`R...L`，则范围内的点会受到来自两侧的冲击，这种情况下`.`的最终状态取决于其与`L`和`R`各自的距离。
- 如果离左侧近，最终状态则为`R`，如果离右侧近，最终状态则为`L`，如果距离相等，那么最终保持为`.`。
- 根据上述理论，我们可以维护两个指针，从左到右遍历dominoes，并按顺序让其对应到每一组非`.`的`L`或`R`的边界，对于边界范围内的点进行相应的变换即可。

```java
class Solution {
    public String pushDominoes(String dominoes) {
        // 由于最左侧和最右侧可能出现非`L`和`R`的情况
        // 我们需要添加边界来处理corner cases
        dominoes = 'L' + dominoes + 'R';
        StringBuilder sb = new StringBuilder();
        
        int i = 0, n = dominoes.length();
        for (int j = 1; j < n; j++) {
            if (dominoes.charAt(j) == '.') continue;
            char l = dominoes.charAt(i), r = dominoes.charAt(j);
            if (i > 0) sb.append(l);
            if (l == r) {
                for (int k = i + 1; k < j; k++) sb.append(l);
            } else if (l == 'L' && r == 'R') {
                for (int k = i + 1; k < j; k++) sb.append('.');
            } else {
                for (int k = i + 1; k < j; k++) {
                    int distToLeft = k - i, distToRight = j - k;
                    if (distToLeft < distToRight) sb.append('R');
                    else if (distToLeft == distToRight) sb.append('.');
                    else sb.append('L');
                }
            }
            i = j;
        }
        return sb.toString();
    }
}
```

## Solution 2: Distance diff

- $TC:O(n)$
- $SC:O(n)$
- 我们还可以通过计算每个位置到最近的`L`和`R`的距离来判断（不过这里是离得越近值越大，且中间有不同方向的骨牌时会被置0）。
- 具体的算法是，先从左到右遍历dominoes，如果遇到`R`则设置当前位置的dist为n，如果遇到`.`则将dist基于之前的值递减（但不低于0），如果遇到`L`则将dist设置为0。
- 然后再从右到左遍历dominoes，根据类似的方法求出每个位置相对于`L`的dist。最终将两次遍历获得的结果做一个向量差，如果dist[i]大于0，说明对当前位置i来说左侧的`R`施加的影响更大，最终状态为`R`。如果dist[i]小于0，则最终状态为`L`。如果dist[i]等于0，则最终状态为`.`。
- 当然，这个向量之差也可以在第二次遍历的过程中直接进行计算。比如我们有S = 'R.R...L'，那么从左到右的结果为[7, 6, 7, 6, 5, 4, 0]，从右到左的结果为[0, 0, 0, 4, 5, 6, 7]，最终结果为[7, 6, 7, 2, 0, -2, -7]，对应RRRR.LL。

```java
class Solution {
    public String pushDominoes(String dominoes) {
        int n = dominoes.length(), d = 0;
        int[] dist = new int[n];
        for (int i = 0; i < n; i++) {
            if (dominoes.charAt(i) == 'R') d = n;
            else if (dominoes.charAt(i) == 'L') d = 0;
            else d = Math.max(d - 1, 0);
            dist[i] = d;
        }
        
        for (int j = n - 1; j >= 0; j--) {
            if (dominoes.charAt(j) == 'R') d = 0;
            else if (dominoes.charAt(j) == 'L') d = n;
            else d = Math.max(d - 1, 0);
            dist[j] -= d;
        }
        
        StringBuilder sb = new StringBuilder();
        for (int k = 0; k < n; k++) {
            if (dist[k] > 0) sb.append('R');
            else if (dist[k] == 0) sb.append('.');
            else sb.append('L');
        }
        return sb.toString();
    }
}
```