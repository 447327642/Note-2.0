# Count and Say

出处

报数指的是，按照其中的整数的顺序进行报数，然后得到下一个数。如下所示：

1, 11, 21, 1211, 111221, ...

+ 1 读作 "one 1" -> 11.
+ 11 读作 "two 1s" -> 21.
+ 21 读作 "one 2, then one 1" -> 1211.

给定一个整数 n, 返回 第 n 个顺序。

样例

    给定 n = 5, 返回 "111221".

注意

    整数的顺序将表示为一个字符串。

## Solution

按照规则进行递推即可

## Complexity

时间复杂度 O(n^2 )，空间复杂度 O(1)

## Code

```python
class Solution:
    # @param {int} n the nth
    # @return {string} the nth sequence
    def countAndSay(self, n):
        # Write your code here
        say = '1'
        for i in range(n-1):
            say = self._count_say(say)
        return say

    def _count_say(self, s):
        curr = None
        count = 0
        say = ""
        for c in s:
            if c == curr:
                count += 1
            else:
                if curr:
                    say += str(count)+str(curr)
                curr = c
                count = 1
        say += str(count)+str(curr)
        return say

```


