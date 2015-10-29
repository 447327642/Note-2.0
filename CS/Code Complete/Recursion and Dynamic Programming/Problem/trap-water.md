# Trap Water

出处

You are given an array of n non-negative integers. Each value means the height of a histogram. Suppose you are pouring water onto them, what is the maximum water it can hold between a left bar and a right bar (no seperation)?

## Solution

当前节点的解，取决于左右两边的海拔高度，可以左一遍DP求出左侧海拔高度，再右一遍DP求出右侧海拔高度，同时积累结果：当前节点的储水量，等于左侧最高海拔与右侧最高海拔的较小值减去当前节点的海拔。

## Complexity

两轮遍历，时间复杂度 O(n)，空间复杂度 O(n)

## Code

```java
int maxWater(int[] arr){
	int len = arr.length;
	if (len == 0) return 0;
	
	int leftMax = 0;
	int rightMax = 0;
	int water = 0;
	int[] dp = new int[len];
	// from left
	for (int i = 0; i < len - 1; i++){
		dp[i] = leftMax;
		if (arr[i] > leftMax){
			leftMax = arr[i];
		}
	}
	// from right
	for (int i = len-1; i >= 0; i++){
		if (Math.min(dp[i], rightMax) > arr[i]){
			water += Math.min(dp[i], rightMax) - arr[i];
		}
		if (arr[i] > rightMax){
			rightMax = arr[i];
		}
	}
	
	return water;
}
```
 

