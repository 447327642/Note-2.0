# Valid Parentheses

出处

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is a valid parentheses string. For example, “(([]))” is valid, but “(]” or “((“ is not.

## Solution

比较明显的一件事是，为了判断输入是否有效，我们需要从头到尾地扫描整个字符串。问题在于，对于某个字符，我们并不能立刻判断是否有效，因为当前节点的解依赖后驱节点。这种情况下，我们可以尝试使用栈作为辅助结构： 需要先将当前节点入栈，然后看其后继节点的值，直到其依赖的所有节点都完备时，再从栈中弹出该元素求解。具体地，当当前节点是一个左括号，我们就把它入栈，直到读到其所依赖的右括号，再从栈中弹出该节点。如果当前节点是右括号，则栈顶必须是与之相依赖的左括号，否则输入无效。当扫描完整个字符串，栈应该为空，否则输入无效。

## Complexity

时间复杂度O(n)。辅助栈最多有n个数据入栈，故额外空间复杂度O(n)

## Code

```java
boolean isLeftPart(char c){
	if (c == '(' || c == '[' || c == '{'){
		return true;
	}
	return false;
}

boolean isValidParentheses(String paren){
	Stack<Character> util = new Stack<Character>();
	for (int i = 0; i < paren.length(); i++){
		if (isLeftPart(paren.charAt(i)){
			util.push(paren.charAt(i));
		} else {
			if (util.IsEmpty()){
				return false;
			}
			char cur = util.top();
			boolean match = false;
			if (cur == '(' && paren.charAt(i) == ')'){
				match = true;
			}
			if (cur == '[' && paren.charAt(i) == ']'){
				match = true;
			}
			if (cur == '{' && paren.charAt(i) == '}'){
				match = true;
			}
			
			if (match){
				util.pop();
			} else {
				return false;
			}
			
		}
	}
	return util.isEmpty();
}
```


