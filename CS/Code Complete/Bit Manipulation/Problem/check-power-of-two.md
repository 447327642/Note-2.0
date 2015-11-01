# Check Power of Two

Explain what the following code does: ((n & (n-1)) == 0)

## Solution

n = abcde1000
n - 1 = abcde0111

can only have one 1 in the binary form -> check power of 2

注意判断 n > 0

```java
public class Solution {
    public boolean isPowerOfTwo(int n) {
        return  (n > 0) && (n & (n-1)) == 0;
    }
}
```

