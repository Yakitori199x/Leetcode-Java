# 911. Online Election (2021.03.24)

https://leetcode.com/problems/online-election/

```java
/**
 * Your TopVotedCandidate object will be instantiated and called as such:
 * TopVotedCandidate obj = new TopVotedCandidate(persons, times);
 * int param_1 = obj.q(t);
 */
```

## Solution 1: Binary Search

- $TC:O(n)$ for construction, $O(logn)$ for q method
- $SC:O(n)$
- 思路很直观，首先计算出times中每个时间点的leader（通过一个map来记录候选人的得票数，然后把当前候选人的得票数和当前得票最多的候选人比较）。
- 之后就是通过二分搜索找出时刻t在times中所处的位置，该位置对应的leader就是返回值。

```java
class TopVotedCandidate {
    private int[] leaders;
    private int[] times;
    
    public TopVotedCandidate(int[] persons, int[] times) {
        this.times = times;
        this.leaders = new int[times.length];
        Map<Integer, Integer> votes = new HashMap<>();
        int leader = -1;
        for (int i = 0; i < persons.length; i++) {
            votes.put(persons[i], votes.getOrDefault(persons[i], 0) + 1);
            // 若当前候选人的得票超过了leader，则更新leader
            if (i == 0 || votes.get(persons[i]) >= votes.get(leader)) {
                leader = persons[i];
            }
            // 记录每个times[i]对应的leader
            leaders[i] = leader;
        }
    }
    
    public int q(int t) {
        // built-in的二分搜索，如果在times找到t，返回的i就是t的位置
        // 否则，返回的i = - 插入位置 - 1（插入位置是times中大于t的第一个数）
        int i = Arrays.binarySearch(times, t);
        return i >= 0 ? leaders[i] : leaders[-i - 2];
    }
}
```

## Solution 2: Binary Search

- $TC:O(n)$ for construction, $O(logn)$ for q method
- $SC:O(n)$
- 自己实现的二分搜索。

```java
class TopVotedCandidate {
    private int[] leaders;
    private int[] times;
    
    public TopVotedCandidate(int[] persons, int[] times) {
        this.times = times;
        this.leaders = new int[times.length];
        Map<Integer, Integer> votes = new HashMap<>();
        int leader = -1;
        for (int i = 0; i < persons.length; i++) {
            votes.put(persons[i], votes.getOrDefault(persons[i], 0) + 1);
            if (i == 0 || votes.get(persons[i]) >= votes.get(leader)) {
                leader = persons[i];
            }
            leaders[i] = leader;
        }
    }
    
    public int q(int t) {
        int l = 0, r = times.length;
        while (l < r) {
            int mid = (r - l) / 2 + l;
            if (times[mid] <= t) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        return leaders[l - 1];
    }
}
```