## 71. Simplify Path (2020.06.30)

https://leetcode.com/problems/simplify-path/

## Solution 1: Split and iterate the path

- $TC:O(N)$, N is the length of the path, because of split operation
- $SC:O(n)$, n is the number of dirs/levels in the path
- If meets ".." and stack is not empty, pop the element
- If meets "" and ".", do nothing; otherwise, push the element

```java
class Solution {
    public String simplifyPath(String path) {
        Stack stack = new Stack<>();
        StringBuilder sb = new StringBuilder();
        
        for (String s : path.split("/")) {
            if (s.equals("..")) {
                if (!stack.isEmpty()) stack.pop();
            } else if(!s.equals("") && !s.equals(".")) {
                stack.push(s);
            }
        }

        while (!stack.isEmpty()) {
            sb.insert(0, stack.pop());
            sb.insert(0, "/");
        }
        
        String res = sb.toString();
        return res.equals("") ? "/" : res;
    }
}
```

