## 279. Perfect Squares (2020.10.27)

https://leetcode.com/problems/perfect-squares/

## Solution 1: DP

- $TC:O(N^{3/2})$
- $SC:O(N)$
- dp[i]用于记录数值i所需要的最少个数的完全平方和，初始状态为dp[0] = 0，状态转移方程：dp[i] = min(dp[i - 1_1], dp[i - 2_2], dp[i - 3*3], ...) + 1，即数值i对应的个数应该是dp[i - 小于i的某个完全平方数]对应的个数加一

```java
class Solution {
    public int numSquares(int n) {
        if (n <= 0) return 0;

        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j * j <= i; j++) {
                dp[i] = Math.min(dp[i], dp[i - j*j] + 1);
            }
        }
        
        return dp[n];
    }
}
```

## Solution 2: BFS

- 以n为根结点，按照level-order构造一棵树的同时遍历
- n的子结点为n减去1~n^(1/2)的平方，按照这个规则进行遍历和构造直到结点值为0，此时当前层数就是n对应的完全平方数的个数

```java
class Solution {
    public int numSquares(int n) {
        if (n <= 0) return 0;
        
        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> visited = new HashSet<>();
        int res = 1;
        queue.offer(n);
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int num = queue.poll();
                for (int j = 1; j * j <= num; j++) {
                    int val = num - j * j;
                    if (val == 0) return res;
                    if (!visited.contains(val)) {
                        queue.offer(val);
                        visited.add(val);
                    }
                }
            }
            res++;
        }
        
        return -1;
    }
}
```

## Solution 3: Math (Lagrange's Four-square Theorem)

- $TC:O(N^{(1/2)})$
- $SC:O(1)$
- https://leetcode.com/problems/perfect-squares/discuss/707526/Python-Fastest-O(sqrt(n)
- 根据Lagrange's Four-square Theorem，任意正数均可被分解为四个数的完全平方之和，当然四个数中可能包含0。也就是说，任意正数均可看做1/2/3/4个数的完全平方之和。且当n=4^k(8m+7)时，对应的非零数为4。
- 因此我们可以从小到大判断，先判断n本身是不是完全平方数，然后再判断n是不是两个完全平方数之和。最后判断是否满足n=4^k(8m+7)，不满足则为3。

```java
class Solution {
    public int numSquares(int n) {
        if (n <= 0) return 0;
        
        if (Math.sqrt(n) % 1 == 0) return 1;
        
        for (int i = 0; i * i <= n; i++) {
            if (Math.sqrt(n - i * i) % 1 == 0) return 2;
        }
        
        while (n % 4 == 0) n >>= 2;
        if (n % 8 == 7) return 4;
        
        return 3;
    }
}
```

