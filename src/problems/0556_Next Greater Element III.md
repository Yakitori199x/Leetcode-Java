# 556. Next Greater Element III (2021.03.23)

https://leetcode.com/problems/next-greater-element-iii/

## Solution 1

- $TC:O(n)$
- $SC:O(n)$
- 本题要求的就是比给定的数n来得大且拥有和n同样的digits（构成数的每一个数字）的最小数，换句话说就是n的下一个排列（next permutation）。
- 因为求的是最小数，我们从右到左遍历n的每一位，直到发现某个i能够满足其下一位数字小于当前数字（num[i-1] < num[i])。
- 这样求得的位置i-1就是我们接下来要替换的位置，然后我们再次从右到左，在end->i的范围找到首个大于num[i-1]的位置j，之后交换i-1处和j处的数字。
- 最后，我们需要对[i, end]这一段数字进行升序排序，由于可以肯定这一段的数字是单调递减的，我们可以反转这一段数字得到最终结果。

```java
class Solution {
    public int nextGreaterElement(int n) {
        char[] num = String.valueOf(n).toCharArray();
        int i = num.length - 1;
        // 找到第一个待交换的位置
        while (i > 0 && num[i] <= num[i - 1]) i--;
        if (i == 0) return -1;
        
        // 找到第二个待交换的位置并与i-1交换
        int j = num.length - 1;
        while (num[j] <= num[i - 1]) j--;
        swap(num, i - 1, j);
        
        // 反转从i到end的字符串
        j = num.length - 1;
        while (i < j) {
            swap(num, i++, j--);
        }
        
        // 因为有可能溢出，将结果存储到long再与MAX_INT判断
        long res = Long.valueOf(new String(num));
        return (res > Integer.MAX_VALUE || res <= n) ? -1 : (int) res;
    }
    
    private void swap(char[] num, int i, int j) {
        char temp = num[i];
        num[i] = num[j];
        num[j] = temp;
    }
}
```