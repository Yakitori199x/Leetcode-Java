## 232. Implement Queue using Stacks (2020.06.24)

https://leetcode.com/problems/implement-queue-using-stacks/


```java
/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

## Solution 1: Two Stacks

- $TC:O(N)$ for push(), $O(1)$ for pop(), peek() and empty()
- $SC:O(N)$, specifically, $O(N)$ for push(), $O(1)$ for pop(), peek() and empty()
- s2 is only used in push(), to store the reversed sequence of elements from s1, then add new element and pop all elements in s2 to s1

```java
class MyQueue {

    private Stack<Integer> s1;
    private Stack<Integer> s2;
    
    /** Initialize your data structure here. */
    public MyQueue() {
        this.s1 = new Stack<>();
        this.s2 = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        while (!s1.isEmpty()) s2.push(s1.pop());
        s2.push(x);
        while (!s2.isEmpty()) s1.push(s2.pop());
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        return s1.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        return s1.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty();
    }
}
```

## Solution 2: Two Stacks

- $TC:O(N)$ for pop() in worst case, but can be amortized to $O(1)$, $O(1)$ for pop(), peek() and empty()
- $SC:O(N)$
- Always push new element into s1, and set the element as top if s1 is empty,
- Directly pop from s2 if s2 is not empty, otherwise push elements in s1 to s2 then pop
- So pop will cost O(N) only when latter case happens, and in most cases it costs O(1) -> amortized to O(1)
- For peek(), return top of s2 if s2 is not empty, otherwise return top variable

```java
class MyQueue {

    private Stack<Integer> s1;
    private Stack<Integer> s2;
    private int top;
    
    /** Initialize your data structure here. */
    public MyQueue() {
        this.s1 = new Stack<>();
        this.s2 = new Stack<>();
        this.top = Integer.MIN_VALUE;
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        if (s1.isEmpty()) top = x;
        s1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) s2.push(s1.pop());
        }
        
        return s2.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        if (s2.isEmpty()) return top;
        return s2.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}
```

