# 609. Find Duplicate File in System (2021.04.22)

https://leetcode.com/problems/find-duplicate-file-in-system/

## Solution 1: HashMap

- $TC:O(n*m)$
- $SC:O(n*m)$
- n表示paths中包含的path数，m表示每个path平均包含的字符数。
- 思路很简单，就是解析每一个path，获取到目录的path以及每个文件的文件名和内容。
- 之后就是以content为Key，以path+filename为Value，把所有内容相同的文件放到同一个List中。
- 最后检查这个Map，如果content对应的文件数大于1，说明有重复的文件，那么就把这些文件加入到res中。

```java
class Solution {
    public List<List<String>> findDuplicate(String[] paths) {
        Map<String, List<String>> map = new HashMap<>();
        for (String path : paths) {
            String[] parts = path.split("\\s+");
            for (int i = 1; i < parts.length; i++) {
                int idx = parts[i].indexOf("(");
                String filename = parts[0] + "/" + parts[i].substring(0, idx);
                String content = parts[i].substring(idx);
                if (!map.containsKey(content)) map.put(content, new ArrayList<>());
                map.get(content).add(filename);
            }
        }
        
        List<List<String>> res = new ArrayList<>();
        for (String content : map.keySet()) {
            if (map.get(content).size() > 1) res.add(map.get(content));
        }
        return res;
    }
}
```

## Follow-ups

- 本题的精华其实在于follow-up里的这几个问题，详细的讨论和分析可以参考下面两个链接的评论区。
- 简单概括就是，要在数量众多且体积巨大的文件及文件夹中找到内容完全重复的文件，我们可以递进使用size，hash，byte by byte三种identifier。
- 最先就是根据size来划分这些文件，size不同那么必不可能是相同的文件。
- 接下来对于size相同的多个文件，取文件的同一部分（比如开头，结尾的bytes）进行hash，这样做的好处是节省时间和内存，因为对于大容量文件来说，要hash文件的所有内容是很耗费时间，特别是内存的。
- 如果不同文件的同一部分得到的hash是不同的，那么这些文件肯定不是相同的文件。否则，我们对得到相同hash值的文件进行最彻底的比较，即对其所有内容进行byte by byte的比较，这样就可以最终确定是不是重复的文件，避免了false positive（如果在hash那步就结束的话，可能会出现不同的文件得到相同hash值的情况，这样就会误判断）。
- https://leetcode.com/problems/find-duplicate-file-in-system/discuss/104123/C%2B%2B-clean-solution-answers-to-follow-up
- https://leetcode.com/problems/find-duplicate-file-in-system/discuss/104120/Follow-up-questions-discussion