# 738. Monotone Increasing Digits (2021.03.11)

https://leetcode.com/problems/monotone-increasing-digits/

## Solution 1: Greedy ver1

- $TC:O(logN)$，此处为以10为底的log，logN即N的位数
- $SC:O(logN)$
- 核心思想就是：为了获得小于等于N的非严格单调递增的最大整数，我们在从左至右遍历N的时候，要尽可能地保留每一位数。
- 直到我们初次遇到某位数大于它的下一位数，这时候我们就不得不将这一位减去1，并将其后续位均设为9，比如1234321 -> 1233999。
- 但是，当N[i]被减去1以后，可能就不再满足N[i-1] <= N[i]，比如2333332 -> 233332x。
- 在这种情况下，我们需要处理减1的位置应该是第一个3，即2333332 -> 2299999。因此，我们需要追踪的应当是N[0~i]这一段中的最大数第一次出现的位置。
- 然后用这个最大数去和N[i+1]去比较，如果N[i+1]比当前max小，那就把maxPos处的数值减去1，其后的所有位数置为9即可。

```java
class Solution {
    public int monotoneIncreasingDigits(int N) {
        char[] num = String.valueOf(N).toCharArray();
        int maxPos = 0;
        for (int i = 0; i < num.length - 1; i++) {
            // 追踪从0到i的max首次出现的位置
            if (num[i] > num[maxPos]) maxPos = i;

            // 也可以是num[i] > num[i + 1]
            // 因为num[i]不是当前最大的，就是和当前最大相等的
            if (num[maxPos] > num[i + 1]) {
                num[maxPos] -= 1;
                for (int j = maxPos + 1; j < num.length; j++) {
                    num[j] = '9';
                }
                break;
            }
        }
        return Integer.parseInt(new String(num));
    }
}
```

## Solution 2: Greedy ver2

- $TC:O(logN)$，此处为以10为底的log，logN即N的位数
- $SC:O(logN)$
- 基于Solution1的思想，我们从右到左遍历num，如果发现num[i] < num[i-1]，则将num[i-1]减去1，并将marker设定至i处。
- 这样对于一般的情况，如1434321，marker会被设置到最前端的满足num[i] < num[i-1]的i处。
- 对于像是2333332这样的情况，则会因为3被减去1后使得下一轮的num[i] < num[i-1]（2 < 3)，从而让marker一直往前推进到num[2]的位置。

```java
class Solution {
    public int monotoneIncreasingDigits(int N) {
        char[] num = String.valueOf(N).toCharArray();
        int marker = num.length;
        for (int i = num.length - 1; i > 0; i--) {
            if (num[i] < num[i - 1]) {
                num[i - 1]--;
                marker = i;
            }
        }
        
        for (int i = marker; i < num.length; i++) {
            num[i] = '9';
        }
        return Integer.parseInt(new String(num));
    }
}
```

## Solution 3: Addition

- $TC:O(1)$
- $SC:O(1)$
- 很巧妙的一种解法。我们以143432为例，143432 -> 139999 = 111111 * 1 + 11111 * 2 + 1111 * 6。
- 不难看出，由于最终的答案是非严格单调递增的，那么这个数必然形如a0a1a2...an (1 <= a0 <= a1 <= a2 <= ... <= an <= 9)。
- 可以继续推出a0a1a2...an = a0 * (n+1个1) + (a0-a1) * (n-2个1) ... + (an - an-1) * 1，即res肯定是由多个形如111...1的数字组成。
- 根据本题给出的条件，N的最大值为10^9，说明我们可以从ones=111111111开始对res进行累加。
- 如果res+ones超过N，则将ones减少一位，总累加次数不超过9次。

```java
class Solution {
    public int monotoneIncreasingDigits(int N) {
        int ones = 111111111, res = 0;
        for (int i = 0; i < 9; i++) {
            while (res + ones > N) ones /= 10;
            res += ones;
        }
        return res;
    }
}
```