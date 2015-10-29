# First Common Ancestor

出处

## Solution

如果有指向父节点的指针，那么就一直上溯，直到找到一样的父节点。如果没有指向父节点的指针，那么从根节点开始用DFS选择性的进行搜索：如果这两个节点都在某个节点的左子树中，那么解一定在此节点的左子树中；类似的，如果两个节点都在某个节点的右子树中，那么解一定在此节点的右子树中。换言之，所求的解一定将给定的两个节点分别分割在左右子树中。

那么，我们可以用一个辅助函数来判断一个节点是否隶属于子树。在主函数中，从根开始判断两个节点是否处于同一边的子树：如果是，那么所求的解一定属于该子树， 我们可以沿子树方向再往下走一层；如果不是，那么当前根就是答案。

## Complexity

假设树高度为h，对于第i层，判断两个节点是否分别处于不同子树需要搜索2^(h-i)次。整体复杂度为：1 + 2 + 2^2 + … + 2^h， 即O(2^(h+1))。

## Code

```java
Node commonAncestor(Node root, Node node1, Node node2){
	if (root == null || node1 == null || node2 == null) return null;
	
	if (covers(root.left, node1) && covers(root.left, node2)){
		return commonAncestor(root.left, node1, node2);
	}
	if (covers(root.right, node1) && covers(root.right, node2)){
		return commonAncestor(root.right, node1, node2);
	}
	return root;
}


boolean covers(Node root, Node node){
	if (root == null) return false;
	if (root == node) return true;
	
	return covers(root.left, node) || covers(root.right, node);
}

```



