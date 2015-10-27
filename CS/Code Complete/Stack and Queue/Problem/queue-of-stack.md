# Queue of Stack

出处

Given a stack structure, use it to implement a queue.

## Solution

这是一道比较常见的题目。 栈的输出顺序是LIFO，queue的输出顺序是FIFO，考虑到如果想利用栈实现特定顺序的读取操作，往往可以借助两个栈互相“倾倒”来实现特定顺序：当一个栈中的元素倾倒到另一个栈中，则原栈最后出栈的元素最先出栈，相当于实现了FIFO。

## Complexity

出栈由于多了倾倒的过程，故时间复杂度降为O(n)。空间复杂度不变，因为并没有重复存储元素。

## Code

```java
class StackQueue{
	private Stack<Integer> inputStack;
	private Stack<Integer> outputStack;
	
	public void enqueue(int value){
		inputStack.push(value);
	}
	
	public int dequeue(){
		int value;
    	if (!outputStack.isEmpty()) {
    		value = outputStack.top();
    		outputStack.pop();
    		return value;
    	}
    	while (!inputStack.empty()) {
    		outputStack.push(inputStack.top());
    		inputStack.pop();
    	}
    	value = outputStack.top();
    	outputStack.pop();
    	return value;
	}
}
```

