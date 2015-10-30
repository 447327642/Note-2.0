# Big O

需要了解如何分析 Time Complexity 以及 Space Complexity.

+ 了解递归情况下的复杂度分析
	+ 根据递推公式模拟几步，然后找到规律进行分析
	+ When you have a recursive function that makes multiple calls, the runtime will often look like O(branches^depth)
+ Big O just describes the rate of increase.
+ Drop the Constants
+ Drop the Non-Dominant Terms
	+ `O(X!)` > `O(2^x)` > `O(x^2)` > `O(x log x)` > `O(x)` > `O(log x)`
+ do this, then, when done, do that -> add the runtime
+ do this for each time you do that -> multiply the runtime
+ Log N Runtime 来自于二分查找或者诸如二叉树等结构

## 例题

> Suppose we had an algorithm that took in an array of strings, sorted each string, and then sorted the full array. What would the runtime be?

+ Let `s` be the length of the longest string
+ Let `a` be the length of the array

The runtime can be get as followed:

+ Sorting each string is `O(s log s)`
+ We have to do this fro every string, `O(a*s log s)`
+ Now we have to sort all the strings. Need to campare the strings. Each string comparison takes `O(s)` time. There are `O(a log a)` comparisons, therefore this will take `O(a*s log a)` time
+ Add up these two parts, you get `O(a*s(log a + log s))`

> The following simple code sums the values of all the nodes in a balanced binary search tree. What is its runtime?

	int sum(Node node){
	    if (node == null){
	        return 0;
	    }
	    return sum(node.left) + node.value + sum(node.right);
	}

*有树结构并不意味着 runtime 中一定会有 log*

The runtime will be linear in terms of the number of nodes. If there are N nodes, then the runtime is O(N)

> What is the time complexity of this function?

	boolean isPrime(int n){
	    for (int x = 2; x * x <= n; x++){
	        if (n % x == 0){
	            return false;
	        }
	    }
	    return true;
	}

The for loop will start when `x = 2` and end when `x * x = n`, aka √n. So it runs in O(√n) time.

> This code prints all permutations of a string. What is its time complexity?

	void permuation(String str){
	    permutation(str, "");
	}

	void permutation(String str, String prefix){
	    if (str.length() == 0){
	        System.out.println(prefix);
	    }
	    else {
	        for (int i = 0; i < str.length(); i++){
	            String rem = str.substring(0,i) + str.substring(i+1);
	            permutation(rem, prefix + str.charAt(i));
	        }
	    }
	}

可以有两种不同的思路: What It Means 和 What It Does.

**What It Means**

因为是求排列，如果一个字符串有`n`个字符，那么所有的可能为`n*(n-1)*...*2*1` -\> `O(n!)`

**What It Does**

设一共有`n`个字符，第一次循环，有`n`次递归调用，第二次有`n-1`次，到最后一共有`n*(n-1)*...*2*1` -\> `O(n!)`

> The following code computes the Nth Fibonacci number

	int fib(int n){
	    if (n <= 0) return 0;
	    else if (n == 1) return 1;
	    return fib(n-1) + fib(n-2);
	}

There are 2 branches per call, and we go as deep as N, there fore the runtime is O(2^N)

**Generally speaking, when you see an algorithm with multiple recursive calls, you're looking at exponential runtime**

> The following code prints all Fibonacci numbers from 0 to n. What is its time complexity?

	void allFib(int n){
	    for (int i = 0; i < n; i++){
	        System.out.println(i + ": " + fib(i));
	    }
	}

	int fib(int n){
	    if (n <= 0) return 0;
	    else if (n == 1) return 1;
	    return fib(n-1) + fib(n-2);
	}

这题的特点是，循环中的递归次数因为`i`的值的不同是不同的。例如：

	fib(1) -> 2^1 steps
	fib(2) -> 2^2 steps
	fib(3) -> 2^3 steps
	fib(4) -> 2^4 steps
	...
	fib(n) -> 2^n steps

所以把这些加起来的总和为：`2^1 + 2^2 + 2^3 +...+ 2^n = 2^(n+1)` -\> O(2^n)

> The following code performs integer idvision. What is its runtime (assume a and b are both positive)?

	int div(int a, int b){
	    int count = 0;
	    int sum = b;
	    while (sum <= a){
	        sum += b;
	        count++;
	    }
	    return count;
	}

O(a/b). The variable count will eventually equal 1/b. The while loop iterates count times. Therefore, it iterates a/b times.

