# 基础知识

链表(linked list)是一种常见的线性数据结构。对于单向链表(singly linked list)，每个节点有一个next指针指向后一个节点，还有一个成员变量用以存储数值；对于双向链表(doubly Linked List)，还有一个prev指针指向前一个节点。与数组类似，搜索链表需要O(n)的时间复杂度，但是链表不能通过常数时间(O(1))读取第k个数据。链表的优势在于能够以较高的效率在任意位置插入或删除一个节点。

## 解题策略

+ 当涉及对头节点的操作，我们不妨考虑创建哑节点。
+ 由于题目涉及在链表中寻找特定位置，我们用两个指针变量以不同的速度遍历该链表。
+ 循环遍历链表, 每次只处理当前指针的next 变量，由此实现链表的逆转。

## 链表的基本操作

凡是修改单向链表的操作，只需考虑：

1. 哪个节点的next指针会受到影响，则需要修正该指针；
2. 如果待删除节点是动态开辟的内存空间，则需要释放这部分空间(C/C++)

毕竟，一个链表节点，无非是包含value和next这两个成员变量的数据结构而已。对于双向链表，类似的，则只需额外考虑谁的prev指针会受到影响。

举例如下：

```
void delNode(ListNode *prev) {
  ListNode *curr = prev->next;
  // 删除curr节点只会使prev节点的next受到影响
  prev->next = curr->next;    
  delete curr;    // 清理trash指针
}
```

注：操作链表时务必注意边界条件：curr == head, curr == tail 或者 curr == NULL


## 工具箱

“对于利用链表解决问题，而非解构链表的问题，可以考虑使用标准库。

对C++，

双链表(Doubly linked list)的实现类是`std::list<T>`.

常用`iterator： begin(), end(), rbegin(), rend()`.

常用函数:

    empty(), size(), push_back(T value), pop_back(T value);
    erase(iterator pos), insert(iterator pos, T value);

对于Java,

双链表的实现类是 `LinkedList<E>`

常用函数：

    add(E e), add(int index, E element), remove(int index),
    addAll(Collection<? Extends E> c),  get(int index),

