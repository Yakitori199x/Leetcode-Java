# 1487. Making File Names Unique (2021.04.21)

https://leetcode.com/problems/making-file-names-unique/

## Solution 1: HashMap

- $TC:O(n*l)$
- $SC:O(n*l)$
- n表示names的个数，l表示name的长度。

```java
class Solution {
    public String[] getFolderNames(String[] names) {
        String[] res = new String[names.length];
        // map用来存储name到下一次再遇到name时应该添加的suffix的数值
        // 当然，由于[gta,gta(1),gta,...]这样的情况存在，我们在获得suffix并生成可能的新name后
        // 还需要检查生成的name是否已经被占用，如果已经被占用，就需要递增suffix直到发现一个合理的值
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < names.length; i++) {
            String name = names[i];
            if (map.containsKey(name)) {
                int suffix = map.get(name);
                while (map.containsKey(name + "(" + suffix + ")")) {
                    suffix++;
                }
                // 需要将新生成的name也加入到map，同时更新name对应的suffix，这样下次再遇到name就无需从头再来
                map.put(name + "(" + suffix + ")", 1);
                map.put(name, suffix + 1);
                res[i] = name + "(" + suffix + ")";
            } else {
                map.put(name, 1);
                res[i] = name;
            }
        }
        return res;
    }
}
```