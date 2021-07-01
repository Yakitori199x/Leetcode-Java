# 241. Different Ways to Add Parentheses (2021.02.25)

https://leetcode.com/problems/different-ways-to-add-parentheses/

## Solution 1: Divide-and-conquer, recursion

- $TC:catalan number$，推导见：https://people.math.sc.edu/howard/Classes/554b/catalan.pdf
- $SC:O(n)$
- 本题的基本思想就是分治，一般由三个步骤组成：分解大问题为子问题，分别对子问题进行处理和求解，合并子问题的解到上一级的大问题。
- 分解：要求得对input的在不同group方式下的算术解，就可以想到将input按运算符分解成左右两部分
- 比如对于[2*3-4*5]，可以有[(2)*(3-4*5)]，[(2*3)-(4*5)]，[(2*3-4)*(5)]等等。
- 求解：在求解子问题时，如果已经到达base case，可以直接返回答案。
- 比如本题就是当input为不包含运算符的纯数字时，问题的解就是input的数值，如[(2)]。
- 但是，如果子问题的input还包含运算符，则按相同规则继续分解子问题为更小的子问题去求解，如[(3-4*5)]。
- 合并：当获得left和right两个子问题的解后，将两个解集中的数值(a,b)两两相乘，每个乘积就是当前问题的一个解，这就是合并的过程。
- 当前问题可能又是某个问题的子问题，继续把当前问题的解集返回到父问题中，直到递归过程结束。
- 一个对该题的不错的分析：https://leetcode-cn.com/problems/different-ways-to-add-parentheses/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-5/

```java
class Solution {
    public List<Integer> diffWaysToCompute(String input) {
        if (input == null || input.length() == 0) return new ArrayList<>();
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < input.length(); i++) {
            char ch = input.charAt(i);
            if (ch == '+' || ch == '-' || ch == '*') {
                List<Integer> left = diffWaysToCompute(input.substring(0, i));
                List<Integer> right = diffWaysToCompute(input.substring(i + 1, input.length()));
                for (int a : left) {
                    for (int b : right) {
                        res.add(calculate(a, ch, b));
                    }
                }
            }
        }
        
        if (res.size() == 0) {
            res.add(Integer.valueOf(input));
        }
        
        return res;
    }
    
    private int calculate(int a, char op, int b) {
        switch (op) {
            case '+':
                return a + b;
            case '-':
                return a - b;
            case '*':
                return a * b;
        }
        
        return -1;
    }
}
```

## Solution 2: Divide-and-conquer, memorization

- $TC:uncertain$
- $SC:O(n^2)$
- 可以发现Solution1的递归过程中包含了很多重复求解的子问题，我们可以将其记忆下来。

```java
class Solution {
    private Map<String, List<Integer>> memo = new HashMap<>();
    
    public List<Integer> diffWaysToCompute(String input) {
        if (input == null || input.length() == 0) return new ArrayList<>();
        
        if (memo.containsKey(input)) {
            return memo.get(input);
        }
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < input.length(); i++) {
            char ch = input.charAt(i);
            if (ch == '+' || ch == '-' || ch == '*') {
                List<Integer> left = diffWaysToCompute(input.substring(0, i));
                List<Integer> right = diffWaysToCompute(input.substring(i + 1, input.length()));
                for (int a : left) {
                    for (int b : right) {
                        res.add(calculate(a, ch, b));
                    }
                }
            }
        }
        
        if (res.size() == 0) {
            res.add(Integer.valueOf(input));
        }
        
        memo.put(input, res);
        return res;
    }
    
    private int calculate(int a, char op, int b) {
        switch (op) {
            case '+':
                return a + b;
            case '-':
                return a - b;
            case '*':
                return a * b;
        }
        
        return -1;
    }
}
```

## Solution 3: DP

- $TC:catalan number$
- $SC:O(n)$

```java
https://leetcode-cn.com/problems/different-ways-to-add-parentheses/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-5/
```