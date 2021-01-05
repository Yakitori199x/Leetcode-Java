## 345. Reverse Vowels of a String (2020.07.26)

https://leetcode.com/problems/reverse-vowels-of-a-string/

## Solution 1: Two pointers

- $TC:O(n)$
- $SC:O(n)$
- Use Arrays.asList to create a vowels set in one line

```java
class Solution {
    public String reverseVowels(String s) {
        if (s == null || s.length() == 0) return s;
        Set<Character> set = new HashSet<>(Arrays.asList('a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'));
        
        int i = 0, j = s.length() - 1;
        char[] chars = s.toCharArray();
        while (i < j) {
            if (set.contains(chars[i]) && set.contains(chars[j])) {
                char tmp = chars[i];
                chars[i++] = chars[j];
                chars[j--] = tmp;
            } else if (!set.contains(chars[i])) {
                i++;
            } else {
                j--;
            }
        }
        
        return String.valueOf(chars);
    }
}
```

