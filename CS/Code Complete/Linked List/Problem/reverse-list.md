# Reverse List

出处

Reverse the linked list and return the new head.

## Solution

循环遍历链表, 每次只处理当前指针的next 变量，由此实现链表的逆转。

## Complexity

时间复杂度 O(n)

## Code

```java
Node reverseList(Node head){
	if (head == null) return null;
	
	Node prev = null;
	Node next = null;
	
	while (head.next != null){
		next = head.next
		head.next = prev;
		
		prev = head;
		head = next;
	}
	return head;
}
```


