# Reverse Binary Tree

样例

      1         1
     / \       / \
    2   3  => 3   2
       /       \
      4         4

挑战

    递归固然可行，能否写个非递归的？

## Solution

基本翻转即可

## Complexity

时间复杂度 O(n)，空间复杂度 非递归O(1) 递归O(h) 

## Code 

递归版本

```python
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""
class Solution:
    # @param root: a TreeNode, the root of the binary tree
    # @return: nothing
    def invertBinaryTree(self, root):
        if root is None:
            return None
        if root.left:
            self.invertBinaryTree(root.left)
        if root.right:
            self.invertBinaryTree(root.right)
        root.left, root.right = root.right, root.left
        return root

```

非递归版本

```python
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""
class Solution:
    # @param root: a TreeNode, the root of the binary tree
    # @return: nothing
    def invertBinaryTree(self, root):
        if root is None:
            return None
        queue = [root]
        while queue:
            front = queue.pop(0)
            if front.left:
                queue.append(front.left)
            if front.right:
                queue.append(front.right)
            front.left, front.right = front.right, front.left
        return root

```

