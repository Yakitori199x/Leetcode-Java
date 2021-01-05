## 225. Implement Stack using Queues (2020.06.21)

https://leetcode.com/problems/implement-stack-using-queues/


```java
/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * boolean param_4 = obj.empty();
 */
```

## Solution 1: Two queues - ver 1.0

- $TC:O(N)$ for push(), $O(1)$ for push(), top() and empty()
- $SC:O(N)$, N is the number of elements in the stack
- Push: Always add the new element to q1
- Pop: The element needs to be removed is in the end of q1, but Queue only supports FIFO.
So it is necessary to move all the elements to q2 except last one to be returned.
Also, we need to record the top value of the stack when moving.
Finally, swap q1 and q2 to make sure only q1 stores elements for other operations.

```java
class MyStack {

    Queue<Integer> q1;
    Queue<Integer> q2;
    int top;
    
    /** Initialize your data structure here. */
    public MyStack() {
        this.q1 = new LinkedList<>();
        this.q2 = new LinkedList<>();
        this.top = Integer.MIN_VALUE;
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        q1.add(x);
        top = x;
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        if (q1.size() == 0) return Integer.MIN_VALUE;
        
        while (q1.size() > 1) {
            top = q1.remove();
            q2.add(top);
        }
        
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
        return q2.remove();
        
    }
    
    /** Get the top element. */
    public int top() {
        return top;
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return q1.size() == 0;
    }
}
```

## Solution 2: Two queues - ver 2.0

- $TC:O(N)$ for push(), $O(1)$ for pop(), top() and empty()
- $SC:O(N)$, N is the number of elements in the stack
- Push: Always add new element to q2, then move all the elements in q1 to q2. Then swap q1 and q2, so q2 is always empty in the end, and q1 can keep the newest element in the head of it
- Pop: Remove the element in the head of q1

```java
class MyStack {
    
    Queue<Integer> q1;
    Queue<Integer> q2;
    
    /** Initialize your data structure here. */
    public MyStack() {
        this.q1 = new LinkedList<>();
        this.q2 = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        q2.add(x);
        
        while (!q1.isEmpty()) {
            q2.add(q1.remove());
        }
        
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        if (q1.size() == 0) return Integer.MIN_VALUE;
        return q1.remove();
    }
    
    /** Get the top element. */
    public int top() {
        return q1.peek();
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return q1.isEmpty();
    }
}
```

## Solution 3: One queue

- $TC:O(N)$ for push(), $O(1)$ for pop(), top() and empty()
- $SC:O(N)$, N is the number of elements in the stack
- Push: Always add new element to q, then move and add all the elements in q except the newest one. So q can keep the newest element in the head of it
- Pop: Remove the element in the head of q

```java
class MyStack {
    
    Queue<Integer> q;
    
    /** Initialize your data structure here. */
    public MyStack() {
        this.q = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        q.add(x);
        
        int size = q.size() - 1;
        for (int i = 0; i < size; i++) {
            q.add(q.remove());
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        if (q.isEmpty()) return Integer.MIN_VALUE;
        return q.remove();
    }
    
    /** Get the top element. */
    public int top() {
        if (q.isEmpty()) return Integer.MIN_VALUE;
        return q.peek();
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return q.isEmpty();
    }
}
```

