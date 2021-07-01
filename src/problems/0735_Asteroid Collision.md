# 735. Asteroid Collision (2021.03.22)

https://leetcode.com/problems/asteroid-collision/

## Solution 1: Stack

- $TC:O(n)$
- $SC:O(n)$

```java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        int n = asteroids.length;
        Deque<Integer> stack = new LinkedList<>();
        for (int asteroid : asteroids) {
            if (asteroid > 0) {
                // 若当前的陨石为正，则直接入栈，因为不可能与已经遍历过的陨石相撞
                stack.push(asteroid);
            } else {
                // 否则，当前陨石的值小于0，存在相撞的可能
                // 若栈非空且栈顶陨石为正（不存在重量为0的陨石）且栈顶陨石的重量小于当前陨石的重量
                // 则栈顶陨石被撞碎，弹出栈顶陨石直至栈顶为负，或者栈顶陨石重量大于等于当前陨石重量
                while (!stack.isEmpty() && stack.peek() > 0 && stack.peek() < -asteroid) {
                    stack.pop();
                }
                // 如果栈中还有陨石，则看看栈顶陨石重量是否等于当前陨石
                // 若等于，则栈顶陨石和当前陨石均粉碎
                // 否则，如果栈为空或栈顶为负方向运行的陨石，直接将当前陨石入栈
                // 再否则，栈顶陨石重量比当前陨石来得大，当前陨石粉碎，什么都不用做
                if (!stack.isEmpty() && stack.peek() == -asteroid) stack.pop();
                else if (stack.isEmpty() || stack.peek() < 0) stack.push(asteroid);
            }
        }
        
        int[] res = new int[stack.size()];
        for (int i = stack.size() - 1; i >= 0; i--) {
            res[i] = stack.pop();
        }
        return res;
    }
}
```