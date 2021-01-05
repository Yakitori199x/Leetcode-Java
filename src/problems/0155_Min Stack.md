## 155. Min Stack (2020.06.18)

https://leetcode.com/problems/min-stack/


```java
/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

## Solution 1: Two stacks

- $TC:O(1)$ for each operation
- $SC:O(N)$, N is the number of elements in the stack
- One for elements, the other for minimums
- Use min to store current minimum, and the top of mins stack is the second minimum

```java
class MinStack {

    private Stack<Integer> elements;
    private Stack<Integer> mins;
    private int min;
    
    /** initialize your data structure here. */
    public MinStack() {
        this.elements = new Stack<>();
        this.mins = new Stack<>();
        this.min = Integer.MAX_VALUE;
    }
    
    public void push(int x) {
        elements.push(x);
        if (x <= min) {
            mins.push(min);
            min = x;
        }
    }
    
    public void pop() {
        if (!elements.isEmpty()) {
            if (min == elements.pop()) 
                min = mins.pop();
        }
    }
    
    public int top() {
        if (!elements.isEmpty()) return elements.peek();
        else return Integer.MIN_VALUE;
    }
    
    public int getMin() {
        return min;
    }
}
```

## Solution 2: Two stacks

- $TC:O(1)$ for each operation
- $SC:O(N)$, N is the number of elements in the stack
- One for elements, the other for minimums
- The top of mins stack is the minimum

```java
class MinStack {

    private Stack<Integer> elements;
    private Stack<Integer> mins;
    
    /** initialize your data structure here. */
    public MinStack() {
        this.elements = new Stack<>();
        this.mins = new Stack<>();
    }
    
    public void push(int x) {
        elements.push(x);
        if (mins.isEmpty() || x <= mins.peek()) {
            mins.push(x);
        }
    }
    
    public void pop() {
        if (!elements.isEmpty()) {
            if (mins.peek().equals(elements.pop()))
                mins.pop();
        }
    }
    
    public int top() {
        if (!elements.isEmpty()) return elements.peek();
        else return Integer.MIN_VALUE;
    }
    
    public int getMin() {
        if (!mins.isEmpty()) return mins.peek();
        else return Integer.MAX_VALUE;
    }
}
```

## Solution 3: One stack

- $TC:O(1)$ for each operation
- $SC:O(N)$, N is the number of elements in the stack
- Use one stack to store both elements and past minimums
- Equal to merge two stacks in the first workaround

```java
class MinStack {

    private Stack<Integer> stack;
    private int min;
    
    /** initialize your data structure here. */
    public MinStack() {
        this.stack = new Stack<>();
        this.min = Integer.MAX_VALUE;
    }
    
    public void push(int x) {
        if (x <= min) {
            stack.push(min);
            min = x;
        }
        
        stack.push(x);
    }
    
    public void pop() {
        if (!stack.isEmpty()) {
            if (min == stack.pop())
                min = stack.pop();
        }
    }
    
    public int top() {
        if (!stack.isEmpty()) return stack.peek();
        else return Integer.MIN_VALUE;
    }
    
    public int getMin() {
        return min;
    }
}
```

## Solution 4: Implement from scratch (LinkedList)

- $TC:O(1)$ for each operation
- $SC:O(N)$, N is the number of elements in the stack
- From encapsulation view, it is better to implement a normal stack first, then implement minStack based on it

```java
class MinStack {

    private Node elements;
    
    /** initialize your data structure here. */
    public MinStack() {
        this.elements = null;
    }
    
    public void push(int x) {
        if (elements == null) {
            elements = new Node(x, x, null);
        } else {
            elements = new Node(x, Math.min(x, elements.currentMin), elements);
        }
    }
    
    public void pop() {
        if (elements != null) elements = elements.next;
    }
    
    public int top() {
        if (elements != null) return elements.val;
        else return Integer.MIN_VALUE;
    }
    
    public int getMin() {
        return elements.currentMin;
    }
    
    private class Node {
        
        public int val;
        public int currentMin;
        public Node next;
        
        public Node(int val, int currentMin, Node next) {
            this.val = val;
            this.currentMin = currentMin;
            this.next = next;
        }
    }
}
```

