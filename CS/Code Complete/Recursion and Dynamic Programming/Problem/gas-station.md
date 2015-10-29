# Gas Station

出处

Suppose you are traveling along a circular route. On that route, we have N gas stations for you, where the amount of gas at station i is gas[i]. Suppose the size of the gas tank on your car is unlimited. To travel from station i to its next neighbor will cost you cost[i] of gas. Initially, your car has an empty tank, but you can begin your travel at any of the gas stations. Please return the smallest starting gas station's index if you can travel around the circuit once, otherwise return -1.

## Solution

这是一个比较难理解的问题，但其本质上还是选择一个序列，只不过这个序列是环形的。事实上，可以考虑对问题进行如下操作：对于第i个加油站，它能够给车子提供的净动力为array[i] = gas[i] – cost[i]。问题转化为，找到一个起始位置index，将array依此向左shift，即index->0(index对应新的数组下标0)，index+1->1…，使得对于任意0 <= i < n，满足序列和subSum(0, i)大于0。

首先，考虑什么情况下有解。经过上述转换，很明显有解的情况对应于sum(array)大于等于0。

那么，剩下的问题是在有解的情况下，如何选择一个正确的起始点。类似与一般序列问题，考虑将当前节点作为序列的末节点。如果从记录的开始节点(index)起，到当前节点的过程中，一旦出现subSum小于0，那么从开始节点到当前节点的所有节点都不能作为开始节点，因为在过程中一定会出现subSum小于0的情况，否则累计的结果不会为负。那么，开始点至少是index+1。另一方面，可以证明，如果从记录的开始点出发可以走到第n个加油站，即subSum(index, n)大于0，那么该开始点一定能走完全程。

## Complexity

时间复杂度 O(n)，空间复杂度 O(n)

## Code 

```java
int canCompleteCircuit(int[] gas, int[] cost){
	int len = gas.length;
	if (len != cost.length || len == 0) return -1;
	
	int[] arr = new int[len];
	int sum = 0;
	for (int i = 0; i < len; i++){
		arr[i] = gas[i] - cost[i];
		sum += arr[i];
	}
	
	if (sum < 0){
		return -1;
	}
	
	sum = 0;
	int index = 0;
	for (int i = 0; i < len; i++){
		sum += arr[i];
		if (sum < 0){
			sum = 0;
			index = i + 1;
		}
	}
	return index;
}

```


