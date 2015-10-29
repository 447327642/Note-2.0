# Replace With Product

出处

Given an array of integers, write a function to replace each element with the product of all elements other than that element.

## Solution

当前节点的解，既和左边的元素有关，又与右边的元素有关，两者相互独立，可以用双向DP。左遍历DP计算积累到目前为止的乘积，右遍历DP计算从目前开始到最后的乘积。

## Complexity

两次遍历，时间复杂度 O(n)，空间复杂度 O(n)

## Code  

```java
int[] replaceWithProduct(int[] arr){
	int len = arr.length;
	if (len == 0) return null;
	
	int product = 1;
	int[] dp = new int[len];
	for (int i = 0; i < len - 1; i++){
		dp[i] = product;
		product *= arr[i];
	}
	
	product = 1;
	for (int i = len-1; i >= 0; i++){
		dp[i] = dp[i]*product;
		product *= arr[i];
	}
	
	return dp;
}
```


