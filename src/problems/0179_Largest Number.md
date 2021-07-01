# 179. Largest Number (2021.06.27)

https://leetcode.com/problems/largest-number/

## Solution 1: Custom Comparator
- $TC:O(k*n*logn)$，n为nums的长度，k为nums中的数值对应的字符串的平均长度
- $SC:O(k*n)$
- 对应n1和n2来说，只有两种可能的连接组合，即s1 = n1n2或者s2 = n2n1。
- 所以只要确定s1和s2的相对大小，就可以确定n1应该排在n2之前还是之后，该方法可以通过自定义comparator来实现。

```java
class Solution {
    public String largestNumber(int[] nums) {
        if (nums == null || nums.length == 0) return "";
        
        int n = nums.length;
        String[] strs = new String[n];
        for (int i = 0; i < n; i++) strs[i] = String.valueOf(nums[i]);
        
        /*
         * 这里的Comparator因为需要不断创建连接后的字符串，所以会比较消耗内存，触发更多的GC
         * 如果想要优化内存的使用，可以用两个StringBuilder来存储连接后的字符串，每次更新时清空之前的
         * 然后一位一位地进行比较
         * 比如：
         * sb1.delete(0, sb1.length()).append(str1).append(str2);
         * sb2.delete(0, sb2.length()).append(str2).append(str1);
         * for (int i = 0; i < sb1.length(); i++) {
         *     if (sb1.charAt(i) == sb2.charAt(i)) continue;
         *     return Character.compare(sb2.charAt(i), sb1.charAt(i));
         * }
         * return 0;
         */ 
        Comparator<String> comp = new Comparator<>() {
            @Override
            public int compare(String str1, String str2) {
                // 直接比较两个数拼接后的结果s1和s2
                String s1 = str1 + str2;
                String s2 = str2 + str1;
                return s2.compareTo(s1);
            }
        };
        Arrays.sort(strs, comp);
        // nums中全都是0的情况
        if (strs[0].equals("0")) return "0";
        
        StringBuilder sb = new StringBuilder();
        for (String str : strs) sb.append(str);
        return sb.toString();
    }
}
```