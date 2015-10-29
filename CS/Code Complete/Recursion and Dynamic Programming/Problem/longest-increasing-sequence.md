# Longest Increasing Sequence

出处

Find the longest increasing subsequence in an integer array. E.g, for array {1, 3, 2, 4}, return 3.

## Solution

用DP Table来记录以当前节点为末节点的序列的最大长度，其数值取决于当前节点之前的所有节点：如果当前节点对应的数组数值大于之前的某个节点，那么可以将当前节点对应的数组数值append在该节点的最长序列之后。最终，我们在DP table中将当前节点的结果更新为所有可能解的最大值。递推关系如下:

maxLength(i) = max{ maxLength(k), k = 0~i-1 and array[i] > array[k] } + 1;

另外，如果需要输出最长序列，那么无非就是对于每个节点额外记录一个index，该index是以当前节点为末节点的最长序列中，前驱元素在数组中的下标。

## Complexity

两层循环，时间复杂度 O(n^2 )，空间复杂度 O(n)

## Code  

```java
int lis(int[] arr){
	int len = arr.length;
	int[] dp = new int[len];
	dp[0] = 1;
	int maxLength = 0;
	for (int i = 1; i < dp.length; i++){
		for (int j = 0; j < i; j++){
			if (arr[i] > arr[j] && dp[j] + 1 > dp[i]){
				dp[i] = dp[j]+1;
			} else {
				dp[i] = 1;
			}
		}
	}
	for (int i = 0; i < dp.length; i++){
		if (dp[i] > maxLength){
			maxLength = dp[i];
		}
	}
	return maxLength;
}
```


