# 227. Basic Calculator II (2021.02.25)

https://leetcode.com/problems/basic-calculator-ii/

## Solution 1: Stack

- $TC:O(n)$
- $SC:O(n)$
- 如果算式中只有加号和减号，那么我们很容易地知道用stack去求解。
- 每当新遇到一个运算符时，就可以根据当前运算符将当前求出的num存入stack，最后将stack中的所有num求和即可。
- 本题相比于只存在加减法的情况，又多了乘除法，以及多了对于空格的处理。
- 所以只需要在加减法的基础上加入乘除法相应的操作，即弹出栈顶元素与其做运算后再压入栈。（因为乘除法有更高的优先级）
- 关于算式相关的一系列题目的总结，可以参考：https://leetcode-cn.com/problems/basic-calculator-ii/solution/chai-jie-fu-za-wen-ti-shi-xian-yi-ge-wan-zheng-ji-/

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) return 0;
        
        int num = 0;
        // 设定初始符号为+，作为placeholder
        char op = '+';
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            // 如果是数字则计算构成的数值
            if (Character.isDigit(ch)) {
                num = 10 * num + (ch - '0');
            }
            
            // 否则，当遇到非数字且不是空格的符号（即运算符）或是索引i到达末端时
            // 都需要考虑将当前的num和op进行相应的运算
            if ((ch != ' ' && !Character.isDigit(ch)) || (i == s.length() - 1)) {
                if (op == '+') stack.push(num);
                else if (op == '-') stack.push(-num);
                else if (op == '*') stack.push(stack.pop() * num);
                else stack.push(stack.pop() / num);
                
                // 执行相应的操作后，重置num和op（op重置为本轮循环遇到的新的运算符）
                num = 0;
                op = ch;
            }
        }
        
        int res = 0;
        while (!stack.isEmpty()) res += stack.pop();
        return res;
    }
}
```

## Solution 2: Iterative

- $TC:O(n)$
- $SC:O(1)$
- tempSum表示从开头到当前ch的临时sum
- tempNum表示上轮循环加入tempSum的数值（可能是单纯的一个num，也可能是多次乘法或除法作用后的数值）

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) return 0;
        
        int num = 0, tempNum = 0, tempSum = 0;
        char op = '+';
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (Character.isDigit(ch)) {
                num = 10 * num + (ch - '0');
            }
            
            if ((ch != ' ' && !Character.isDigit(ch)) || (i == s.length() - 1)) {
                if (op == '+') {
                    tempSum += num;
                    tempNum = num;
                } else if (op == '-') {
                    tempSum -= num;
                    tempNum = -num;
                } else if (op == '*') {
                    tempSum -= tempNum;
                    tempSum += tempNum * num;
                    tempNum *= num;
                } else {
                    tempSum -= tempNum;
                    tempSum += tempNum / num;
                    tempNum /= num;
                }
                
                num = 0;
                op = ch;
            }
        }
        
        return tempSum;
    }
}
```