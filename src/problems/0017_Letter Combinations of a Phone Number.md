# 17. Letter Combinations of a Phone Number (2021.02.22)

https://leetcode.com/problems/letter-combinations-of-a-phone-number/

## Solution 1: DFS, backtracking

- $TC:O(n*4^n)$
- $SC:O(n)$

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();
        if (digits == null || digits.length() == 0) return res;
        
        // 不需要创建HashMap，用前两个元素为空字符串的数组来保存即可
        List<String> keys = Arrays.asList(
            "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz");
        StringBuilder sb = new StringBuilder();
        dfs(digits, 0, keys, sb, res);
        return res;
    }
    
    private void dfs(String digits, 
                     int index, 
                     List<String> keys, 
                     StringBuilder sb, 
                     List<String> res) {
        if (index == digits.length()) {
            res.add(sb.toString());
            return ;
        }
        
        String chars = keys.get(digits.charAt(index) - '0');
        for (int i = 0; i < chars.length(); i++) {
            // 用stringBuilder来追踪candidates
            sb.append(chars.charAt(i));
            dfs(digits, index + 1, keys, sb, res);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}
```

## Solution 2: BFS

- $TC:O(n*4^n)$
- $SC:O(n*4^n)$
- 由于digits的每一位对应的字符都是确定的，因此本题也适用BFS。
- 但是像permutation这类题，随着首个加入的元素不同，后续对应的candidates也不同，用BFS来实现就很麻烦。
- 比如，先加入的是[1]，那么后续待加入的就是[2,3,...,n]。如果先加入的是[2]，那么后续则为[1,3,...,n]。

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();
        if (digits == null || digits.length() == 0) return res;
        
        List<String> keys = Arrays.asList(
            "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz");

        Queue<String> queue = new LinkedList<>();
        queue.add("");
        while (!queue.isEmpty()) {
            String base = queue.poll();
            if (base.length() == digits.length()) {
                res.add(base);
                continue;
            }
            
            String candidates = keys.get(digits.charAt(base.length()) - '0');
            for (int i = 0; i < candidates.length(); i++) {
                queue.add(base + candidates.charAt(i));
            }
        }
        
        return res;
    }
}
```