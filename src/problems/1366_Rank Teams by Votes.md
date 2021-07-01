# 1366. Rank Teams by Votes (2021.03.24)

https://leetcode.com/problems/rank-teams-by-votes/

## Solution 1: Sorting Lists

- $TC:O(m*n + n^2*logn)$
- $SC:O(n^2)$
- m为投票人数，即votes的长度。n为队伍数，即votes[i]的长度。
- m*n用于构建每个队伍的得票情况，(n^2 * logn)用于对每个队伍的得票数组进行排序。
- 本题的关键就在于把所有投票者的投票结果votes转换成每支队伍在每个名次上获得的票数counts。
- 之后就是对每支队伍的得票数组进行排序。只不过一般情况下我们排序的对象是整数或者字符，现在变成了数组。
- 而且我们需要定义个性化的排序规则（comparator），规则就是在第i位得票数越多，排序越靠前。
- 如果两个队伍在第i位的得票数相同，则比较下一位来决定谁的位置靠前。如果每一位的得票数都一致，那就由队伍名（字符）顺序决定。
- 详细定义见代码。

```java
class Solution {
    public String rankTeams(String[] votes) {
        if (votes.length == 1) return votes[0];
        
        // 根据votes计算出每支队伍每个名次的得票数counts
        int n = votes[0].length();
        Map<Character, int[]> counts = new HashMap<>();
        for (String vote : votes) {
            for (int i = 0; i < n; i++) {
                char team = vote.charAt(i);
                counts.putIfAbsent(team, new int[n]);
                counts.get(team)[i]++;
            }
        }
        
        // 对teams进行排序，不过排序的依据是team对应的得票数组
        List<Character> teams = new ArrayList<>(counts.keySet());
        // 对两支队伍a和b，从第一位的得票数开始比较
        // 如果当前位的得票数不相等，说明已经可以决出先后
        // 对于Collections.sort来说，返回值为负则把a排在b前面，返回值为正就把b排在a前面。
        // 所以这种不相等的情况我们返回b的得票数-a的得票数
        // 相反地，如果当前位的得票数相等，则延续到下一位比较，直到遍历所有的名次
        // 最后就是比较a和b本身，返回a-b（字典序较小的team排在前面）
        Collections.sort(teams, (a, b) -> {
           for (int i = 0; i < n; i++) {
               if (counts.get(a)[i] != counts.get(b)[i]) {
                   return counts.get(b)[i] - counts.get(a)[i];
               }
           }
           return a - b;
        });
        
        StringBuilder sb = new StringBuilder();
        for (Character team : teams) sb.append(team);
        return sb.toString();
    }
}
```