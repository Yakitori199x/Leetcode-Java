# 946. Validate Stack Sequences (2021.03.16)

https://leetcode.com/problems/validate-stack-sequences/

## Solution 1: Stack, simulation

- $TC:O(n)$
- $SC:O(n)$
- 直接根据pushed和popped来模拟入栈和出栈的过程。
- 我们可以遍历pushed并无条件将元素入栈，当stack非空且栈顶元素等于当前的待pop元素时，说明必须出栈。
- 因为这种情况下如果再不出栈，后续新元素入栈后，出栈的序列就不可能是popped所要求的那样了。
- 出栈的同时，我们也要将当前期待的待pop元素更新（右移popped的index），再次检查栈顶元素和待pop元素的关系。
- 直到两者不相等，说明待pop元素可能存在于还未被push的pushed元素中，从而继续遍历pushed。

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int j = 0, n = pushed.length;
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            stack.push(pushed[i]);
            while (!stack.isEmpty() && stack.peek() == popped[j]) {
                stack.pop();
                j++;
            }
        }
        return stack.isEmpty();
    }
}
```

## Solution 2: When len(pushed) != len(popped)

- $TC:O(n)$
- $SC:O(n)$
- 本题的题设是pushed和popped的长度一致，但是也有可能遇到两者长度不一致的情况。
- 这时我们就要分别维护m和n来分别表示pushed和popped的长度。
- 基本思路还是一样，遍历pushed，不过现在检查待pop元素和栈顶元素的关系时，需要考虑当前的指针j是否大于或等于popped的长度（否则可能超出popped边界）。
- 且最终不是判断stack是否为空，而是检查popped元素是否都被遍历过。
- 因为popped可能少于pushed，即使是合法的序列，最终stack也不会是空的。

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int j = 0, m = pushed.length, n = popped.length;
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < m; i++) {
            stack.push(pushed[i]);
            while (!stack.isEmpty() && j < n && stack.peek() == popped[j]) {
                stack.pop();
                j++;
            }
        }
        return j == n;
    }
}
```

## Solution 3: In-place

- $TC:O(n)$
- $SC:O(1)$
- 直接把pushed用作栈，i为栈指针，j为遍历pushed的指针，k为popped的指针。
- 原理就是stack的元素数肯定不会超过j所在的位置，因此可以把pushed已经遍历过的部分直接拿来用作栈。

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int i = 0, k = 0, n = pushed.length;
        for (int j = 0; j < n; j++) {
            pushed[i] = pushed[j];
            while (i >= 0 && pushed[i] == popped[k]) {
                i--;
                k++;
            }
            i++;
        }
        return i == 0;
    }
}
```