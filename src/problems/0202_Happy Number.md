## 202. Happy Number (2020.08.03)

https://leetcode.com/problems/happy-number/

Premise: 对于这个问题，如果happy number存在，那么最终必定会以1结束（形成单结点环，结点值为1）。但是，如何确保happy number不存在的时候，也会形成环呢？万一数值不断延伸无法形成环呢？对于这一种情况，我们可以注意到，假设初始值n为999（三位数中结果最大的），其squareSum为243，远远小于计算前的值。因此，如果初始值很大，那么计算序列是不断减小，之后可能会有所震荡（比如19->82)，但是肯定不会超过初始值n。这说明就算每一次计算的结果都不是出现过的，最多计算n次之后肯定会落在(1, n]这一范围内，即形成环。
以下链接有更详细的证明过程：
https://leetcode.com/problems/happy-number/discuss/56919/Explanation-of-why-those-posted-algorithms-are-mathematically-valid

## Solution 1: HashSet

- $TC:O(nd)$, d is the number of digits of n
- $SC:O(n)$
- Calculate the squareSum of current number and put it into the set
- If the same value is added before, indicates there is a loop and not happy number
- If the result is 1, the happy number exists

```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> set = new HashSet<>();
        
        int next = n;
        while (set.add(next)) {
            next = squareSum(next);
            if (next == 1) return true;
        }
        
        return false;
    }
    
    private int squareSum(int n) {
        int sum = 0;
        
        while (n != 0) {
            int remainder = n % 10;
            sum += remainder * remainder;
            n = n / 10;
        }
        
        return sum;
    }
}
```

## Solution 2

- $TC:O(nd)$, d is the number of digits of n
- $SC:O(1)$
- It can also be viewed as a Floyd Cycle Detection problem (slow-fast pointers)
- Calculation of squareSum will form a sequence
- The sequence either has a cycle starts with 1 (only one node cycle), that is the case happy number exists, or it has a cycle with other values, if we find a cycle and the value of encounter node is not 1, happy number does not exist

```java
class Solution {
    public boolean isHappy(int n) {
        int slow = n, fast = n;
        
        while (true) {
            slow = squareSum(slow);
            fast = squareSum(squareSum(fast));
            
            if (fast == 1) return true;
            if (fast == slow) return false;
        }
    }
    
    private int squareSum(int n) {
        int sum = 0;
        
        while (n != 0) {
            int remainder = n % 10;
            sum += remainder * remainder;
            n = n / 10;
        }
        
        return sum;
    }
}
```

