# 690. Employee Importance (2021.03.12)

https://leetcode.com/problems/employee-importance/

```java
/*
// Definition for Employee.
class Employee {
    public int id;
    public int importance;
    public List<Integer> subordinates;
};
*/
```

## Solution 1: BFS

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int getImportance(List<Employee> employees, int id) {
        if (employees == null || employees.size() == 0) return 0;
        
        // 由于subordinates只记录了id，在遍历前我们需要构造一个id到employee的map
        Map<Integer, Employee> map = new HashMap<>();
        for (Employee e : employees) map.put(e.id, e);
        
        Queue<Employee> queue = new LinkedList<>();
        queue.offer(map.get(id));
        int res = 0;
        while (!queue.isEmpty()) {
            Employee e = queue.poll();
            res += e.importance;
            for (int subordinate : e.subordinates) {
                queue.offer(map.get(subordinate));
            }
        }
        return res;
    }
}
```

## Solution 2: DFS

- $TC:O(n)$
- $SC:O(h)$

```java
class Solution {
    public int getImportance(List<Employee> employees, int id) {
        if (employees == null || employees.size() == 0) return 0;
        
        Map<Integer, Employee> map = new HashMap<>();
        for (Employee e : employees) map.put(e.id, e);
        
        return dfs(map.get(id), map);
    }
    
    private int dfs(Employee e, Map<Integer, Employee> map) {
        int res = e.importance;
        for (int subordinate : e.subordinates) {
            res += dfs(map.get(subordinate), map);
        }
        return res;
    } 
}
```