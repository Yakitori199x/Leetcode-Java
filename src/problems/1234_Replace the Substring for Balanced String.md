# 1234. Replace the Substring for Balanced String (2021.05.01)

https://leetcode.com/problems/replace-the-substring-for-balanced-string/

## Solution 1: Sliding Window ver1

- $TC:O(n)$
- $SC:O(1)$
- 本题的目的就是找出一个最短的substring，使得替换掉substring中的字符后能让s成为balanced。
- 首先，我们统计s中QWER四个字符各自的出现次数。然后维护一个滑动窗口，其范围对应着可以被替换的substring。
- 我们右移j并减去count中对应字符的出现次数，直到窗口外部分的剩余字符的出现次数均小于或等于k（说明这时候替换掉窗口内的所有字符必定可以满足balanced，但不一定是最短）。
- 然后我们右移i缩小窗口的范围，同时增加对应字符的出现次数直到打破上一条中描述的条件，看看滑动窗口对应的substring可以缩短到什么程度。
- 这时候的窗口就对应着一个可能的最短substring（当然继续右移j并重复以上操作，还有可能在后面的部分找到更短的）。

```java
class Solution {
    public int balancedString(String s) {
        // 直接用长度为128的数组来存储QWER的出现次数
        int[] count = new int[128];
        int i = 0, n = s.length(), res = n, k = n / 4;
        for (char c : s.toCharArray()) count[c]++;
        
        for (int j = 0; j < n; j++) {
            --count[s.charAt(j)];
            // 如果s本身就是balanced的，那么在这个循环的最后时刻，i会越界，所以要判断i是否小于n
            while (i < n && count['Q'] <= k && count['W'] <= k && count['E'] <= k && count['R'] <= k) {
                res = Math.min(res, j - i + 1);
                ++count[s.charAt(i++)];
            }
        }
        
        return res;
    }
}
```

## Solution 2: Sliding Window ver2

- $TC:O(n)$
- $SC:O(1)$
- 该解法和Solution1很相似，就是换个角度思考。
- 我们同样是统计出QWER每个字符的出现次数，但这时候我们直接将其对应的次数减去k。这样一来，减去后出现次数仍大于0的字符，就是我们找到的substring需要包含的部分。
- 举个例子，若k=4，减去k后，Q仍剩余2，W仍剩余1，那说明s中有多余的2个Q和1个W阻碍了s成为balanced。（如果可以替换任意位置的任意字符，那直接替换掉那3个字符即可，但这里我们要找的是substring）
- 所以，我们想要找的substring必须包含了这部分多余的Q和W的（当然也会包含E，R这样没有多余的字符）。
- 同样地，我们用一个滑动窗口来对应要找的substring，同时count中对应字符的次数（这时候count[c]表示的是剩余待寻找的字符数）。
- 如果满足count['Q'] <= 0 && count['W'] <= 0 && count['E'] <= 0 && count['R'] <= 0，则说明当前的窗口囊括了多余的字符，是一个可能的解。
- 剩下的就和Solution1类似，尽可能缩小地窗口从而获得最短的substring。

```java
class Solution {
    public int balancedString(String s) {
        int[] count = new int[128];
        int i = 0, n = s.length(), res = n, k = n / 4;
        for (char c : s.toCharArray()) count[c]++;
        for (int j = 0; j < count.length; j++) count[j] -= k;
        if (isBalanced(count)) return 0;
        
        for (int j = 0; j < n; j++) {
            --count[s.charAt(j)];
            while (isBalanced(count)) {
                res = Math.min(res, j - i + 1);
                ++count[s.charAt(i++)];
            }
        }
        
        return res;
    }
    
    private boolean isBalanced(int[] count) {
        return count['Q'] <= 0 && count['W'] <= 0 && count['E'] <= 0 && count['R'] <= 0;
    }
}
```