# 1311. Get Watched Videos by Your Friends (2021.04.26)

https://leetcode.com/problems/get-watched-videos-by-your-friends/

## Solution 1: Sorting

- 本题的思路很直观，就是先对id用BFS找到第level层的friends，然后再找出这些friends看过的videos，将videos以及其出现的频率保存到map中。
- 最后再对map先根据value的值再根据key的字典序进行排序即可。

```java
class Solution {
    public List<String> watchedVideosByFriends(List<List<String>> watchedVideos, int[][] friends, int id, int level) {
        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> visited = new HashSet<>();
        queue.offer(id);
        visited.add(id);
        
        // BFS找到相应level的friends
        while (level > 0) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int cur = queue.poll();
                for (int next : friends[cur]) {
                    if (!visited.contains(next)) {
                        queue.offer(next);
                        visited.add(next);
                    }
                }
            }
            level--;
        }
        
        // 记录目标friends看过的videos及其频率
        Map<String, Integer> freq = new HashMap<>();
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            for (String video : watchedVideos.get(cur)) {
                freq.put(video, freq.getOrDefault(video, 0) + 1);
            }
        }

        // 因为没有办法直接对map的value进行排序，这里我们先把keys(videos)存储到一个List
        // 然后再对这个list根据给定的comparator进行排序
        List<String> res = new ArrayList<>(freq.keySet());
        Collections.sort(res, (a, b) -> freq.get(a) == freq.get(b) ? a.compareTo(b) : freq.get(a) - freq.get(b));
        return res;
    }
}
```