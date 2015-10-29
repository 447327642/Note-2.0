# Set Bits

出处

Given N and M, 32bit integers, how to set i to j bits (bit position as 1,2,3,…32) of N as the value of bits in M.

For example, N = 00000000000000000000000001111011,

M = 00000000001000000011000000011000, i = 10, j = 20, then the result should be:

00000000001000000011000001111011

## Solution

我们首先根据题意重现我们需要做的操作：

1. 我们需要从M中get第i到第j个比特 
2. 我们需要clear N中第i到第j个比特 
3. 我们需要set N中第i到第j个比特。

对于1)，根据基本操作，get bit需要&1，所以与M进行操作的bit mask在第i到第j位应当为1，其他位为0。对于2)， 根据基本操作，clear bit需要&0，所以与N进行操作的位掩码在第i到第j位应当为0，其他位为1。注意，这个位掩码刚好是前一项操作位掩码的位反运算。对于3)，我们只需要将1)，2)的操作结果进行位或即可。构造所需位掩码的过程如前所述，对~0进行基本操作和加减法即可。

## Complexity

复杂度 O(1)

## Code

```java
final int BITS_COUNT = 32;
int setBits(int m, int n, int i, int j){
	int max = ~0;
	int mask = (max << BITS_COUNT - i) | (max >> j);
	return (m & mask) | (n & ~mask)
}
```

