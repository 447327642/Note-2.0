# Reverse Integer

将一个整数中的数字进行颠倒，当颠倒后的整数溢出时，返回 0 (标记为 32 位整数)。

样例

    给定 x = 123，返回 321
    给定 x = -123，返回 -321

## Solution

注意溢出即可

## Complexity

时间复杂度 O(n)，空间复杂度 O(1)

## Solution

```python
class Solution:
    # @param {int} n the integer to be reversed
    # @return {int} the reversed integer
    def reverseInteger(self, n):
        # Write your code here
        res = 0
        if n >= 0:
            pos = True
        else:
            pos  = False
            n = -n
        while not n == 0:
            if res > 214748364:
                return 0
            else:
                res = res*10 + n%10
                n = n/10
        if pos:
            return res
        else:
            return -res

```

