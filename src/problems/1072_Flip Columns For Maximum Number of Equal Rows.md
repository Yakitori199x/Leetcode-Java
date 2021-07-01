# 1072. Flip Columns For Maximum Number of Equal Rows (2021.04.21)

https://leetcode.com/problems/flip-columns-for-maximum-number-of-equal-rows/

## Solution 1

- $TC:O(m^2*n)$
- $SC:O(n)$
- 时间复杂度为m * (n + m * n) = m * n + m^2 * n -> O(m^2*n)，因为在遍历每一行数组时，我们先要求出这一行的翻转形态flip，然后遍历剩余的行找到与其相同或与其完全相反（即与flip相同）的所有行，比较两个行是否相等需要O(n)。
- 这道题乍看之下是让我们去找如何翻转列，使得翻转后得到的所有元素值都相等的行（目标形态）最多。
- 但实际上我们从行入手进行思考会更简单。首先，我们要明确一点，即对某行row来说，在翻转必要的列使其达到目标形态的情况下，只有翻转前与其相同的行，或是与其完全相反的行，在翻转后才会达到目标形态。而对于其他行，在确保row能够达到目标形态的翻转方式下，都无法通过翻转达到目标形态，除非我们去翻转其他的列。但是，这种情况下row及其相关的行就肯定不是处于目标形态的。
- 因此，我们可以发现，matrix实际上被按照行划分成不同的group，即相同的行以及完全相反的行会构成一个group。
- 而我们要使得翻转某些列后得到的目标形态的行数最大，就转换为找囊括行数最多的group，这样就相当地简单明了。

```java
class Solution {
    public int maxEqualRowsAfterFlips(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length, res = 0;
        // 遍历所有行，找出与其属于同一个group的其他行
        for (int i = 0; i < m; i++) {
            int[] row = matrix[i];
            int[] flip = new int[n];
            int count = 0;
            // 求出当前行翻转后的行
            for (int k = 0; k < n; k++) flip[k] = 1 ^ row[k];
            // 不需要从0开始遍历，其实只要遇到了group的第一个row，那么后续属于同一个group的行都不需要再进行搜索
            // 这里让j从i开始只是省去了一些重复情况，还可以用Set来进一步优化
            for (int j = i; j < m; j++) {
                if (Arrays.equals(matrix[j], row) || Arrays.equals(matrix[j], flip)) {
                    count++;
                }
            }
            res = Math.max(res, count);
        }
        return res;
    }
}
```

## Solution 2: Solution1 Optimized

- $TC:O(m^2*n)$
- $SC:O(max(m,n))$
- 用Set记录已经遍历过的row（包括同属于一个group的其他row）来对Solution1进行优化。

```java
class Solution {
    public int maxEqualRowsAfterFlips(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length, res = 0;
        // set用于记录被遍历过的row对应的行数
        Set<Integer> seen = new HashSet<>();
        for (int i = 0; i < m; i++) {
            // 如果当前的row已经被划分到对应的group，则直接跳过
            if (seen.contains(i)) continue;
            int[] row = matrix[i];
            int[] flip = new int[n];
            int count = 0;
            for (int k = 0; k < n; k++) flip[k] = 1 ^ row[k];
            for (int j = i; j < m; j++) {
                // row j可能属于其他已经被划分好的group，这样的row也无需检查
                if (!seen.contains(j) && (Arrays.equals(matrix[j], row) || Arrays.equals(matrix[j], flip))) {
                    count++;
                    seen.add(j);
                }
            }
            res = Math.max(res, count);
        }
        return res;
    }
}
```

## Solution 3: HashMap

- $TC:O(m*n)$
- $SC:O(m*n)$
- 思路与上面的解法一致，只不过使用了HashMap来保存每个group对应的行数。

```java
class Solution {
    public int maxEqualRowsAfterFlips(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length, res = 0;
        // 这里我们把每一行的值连接起来转换成一个String作为map的key
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < m; i++) {
            StringBuilder sb = new StringBuilder();
            int[] row = matrix[i];
            // 这里的方法很巧妙，如果row[0]为1，那么row对应的String保持不变
            // 如果row[0]为0，那么整个row对应的String就是翻转后的row
            for (int j = 0; j < n; j++) {
                if (row[j] == row[0]) sb.append('1');
                else sb.append('0');
            }
            map.put(sb.toString(), map.getOrDefault(sb.toString(), 0) + 1);
        }
        
        for (int count : map.values()) res = Math.max(res, count);
        return res;
    }
}
```