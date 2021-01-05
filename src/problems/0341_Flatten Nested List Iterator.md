## 341. Flatten Nested List Iterator (2020.06.30)

https://leetcode.com/problems/flatten-nested-list-iterator/


```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return null if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i = new NestedIterator(nestedList);
 * while (i.hasNext()) v[f()] = i.next();
 */
```

## Solution 1:

- $TC:O(N)$ for the whole iteration, N is the number of integers in nestedList
- $SC:O(N)$
- Store elements in the nestedList into a stack in reverse order, so the top of the stack will be the first element of the list.
- hasNext() checks whether the top element is an integer
- -> if it is, it can be return by next()
- -> otherwise, flatten the list(top element) and store elements to the stack

```java
public class NestedIterator implements Iterator<Integer> {

    private Stack<NestedInteger> stack;
    
    public NestedIterator(List<NestedInteger> nestedList) {
        this.stack = new Stack<>();
        flattenList(nestedList);
    }

    private void flattenList(List<NestedInteger> list) {
        for (int i = list.size() - 1; i >= 0; i--) {
            stack.push(list.get(i));
        }
    }
    
    @Override
    public Integer next() {
        if (hasNext()) {
            return stack.pop().getInteger();
        }
        
        return null;
    }

    @Override
    public boolean hasNext() {
        while(!stack.isEmpty() && !stack.peek().isInteger()) {
            List<NestedInteger> list = stack.pop().getList();
            flattenList(list);
        }
        
        return !stack.isEmpty();
    }
}
```

## Solution 2: Recursion

- $TC:O(N)$, N is the number of integers in nestedList
- $SC:O(N)$
- If it is an integer, add to final result
- If it is a list, flatten it

```java
public class NestedIterator implements Iterator<Integer> {

    private List<Integer> flattenResult;
    private int ptr;
    
    public NestedIterator(List<NestedInteger> nestedList) {
        this.flattenResult = new ArrayList<>();
        this.ptr = 0;
        flattenList(nestedList);
    }
    
    private void flattenList(List<NestedInteger> nestedList) {
        if (nestedList == null || nestedList.size() == 0) return;
        
        for (int i = 0; i < nestedList.size(); i++) {
            NestedInteger element = nestedList.get(i);
            if (element.isInteger()) flattenResult.add(element.getInteger());
            else flattenList(element.getList());
        }
    }

    @Override
    public Integer next() {
        if (!hasNext()) return null;
        return flattenResult.get(ptr++);
    }

    @Override
    public boolean hasNext() {
        return ptr != flattenResult.size();
    }
}
```

