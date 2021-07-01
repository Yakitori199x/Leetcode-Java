# 567. Permutation in String (2021.05.06)

https://leetcode.com/problems/permutation-in-string/

## Similar Questions:
- 438. Find All Anagrams in a String

## Solution 1: HashMap + Sliding Window

- $TC:O(n)$
- $SC:O(1)$
- 要解决该问题，我们首先要明确两个点。
- 第一，我们要怎么判断字符串s是字符串t的permutation呢？很简单，只要s和t中的字符相同且出现次数相等，就说明s和t互为permutation。
- 第二，我们要怎么判断s2包含了s1的permutation呢？只需要列举出s2中和s1相同长度的所有子串，然后进行第一点的比较即可。
- 所以，我们可以用一个滑动窗口来标记s2中子串的范围，且每次循环都会添加一个字母并删除一个字母，然后判断hash中的值是否均为0即可。

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        if (m > n) return false;
        
        int[] hash = new int[26];
        for (char c : s1.toCharArray()) hash[c - 'a']++;
        
        for (int i = 0; i < n; i++) {
            hash[s2.charAt(i) - 'a']--;
            if (i - m >= 0) hash[s2.charAt(i - m) - 'a']++;
            if (allZero(hash)) return true;
        }
        return false;
    }
    
    private boolean allZero(int[] hash) {
        for (int i = 0; i < hash.length; i++) {
            if (hash[i] != 0) return false;
        }
        return true;
    }
}
```