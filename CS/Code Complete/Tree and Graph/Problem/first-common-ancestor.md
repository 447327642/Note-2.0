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

来自 CC 的分析与解答

```java
/**
 * Solution #1: With Links to Parents
 *
 * We could trace just p's path upwards. At each node on this path, check to
 * see if this node is on the path from q to the root. The first such node
 * will be the first common ancestor.
 */

TreeNode commonAncestor(TreeNode p, TreeNode q){
    if (p == q) return null;

    TreeNOde ancestor = p;
    while (ancestor != null){
        if (isOnPath(ancestor, q)){
            return ancestor;
        }
        ancestor = ancestor.parent;
    }
    return ull;
}

boolean isOnPath(TreeNode ancestor, TreeNode node){
    while (node != ancestor && node != null){
        node = node.parent;
    }
    return node == ancestor;
}


/**
 * Solution #2: With Links to Parents(Better worst case runtime)
 * We can just traverse upwards from p, storing the parent and the sibling
 * node in a variable. (The sibling node is always a child of parent and
 * refers to the newly uncovered subtree.) At each iteration, sibling gets
 * set to the old parent's sibling node and parent gets set to parent.parent.
 */

TreeNode commonAncestor(TreeNode root, TreeNode p, TreeNode q){
    if (!covers(root, p) || !covers(root, q)){
        return null;
    }
    else if (covers(p, q)){
        return p;
    }
    else if (covers(q, p)){
        return q;
    }

    TreeNode sibling = getSibling(p);
    TreeNode parent = p.parent;
    while (!covers(sibling, q)){
        sibling = getSibling(parent);
        parent = parent.parent;
    }
    return parent;
}

boolean covers(TreeNode root, TreeNode p){
    if (root == null) return false;
    if (root == p) return true;
    return covers(root.left, p) || covers(root.right, p);
}

TreeNode getSibling(TreeNode node){
    if (node == null || node.parent == null){
        return null;
    }

    TreeNode parent = node.parent;
    return parent.left == node ? parent.right : parent.left;
}


/**
 * Solution #3: Without Links to Parents
 *
 * You could follow a chain in which p and q are on the same side. That is,
 * if p and q are both on the left of the node, branch left to look for the
 * common ancestor. If they are both on the right, branch right to look for
 * the common ancestor. When p and q are no longer on the same side, you
 * must have found the first common ancestor.
 */

TreeNode commonAncestor(TreeNode root, TreeNode p, TreeNode q){
    if (!covers(root, p) || !covcers(root, q)){
        return null;
    }
    return ancestorHelper(root, p, q);
}

TreeNode ancestorHelper(TreeNode root, TreeNode p, TreeNode q){
    if (root == null){
        return null;
    }
    else if (root == p){
        return p;
    }
    else if (root == q){
        return q;
    }

    boolean pIsOnLeft = covers(root.left, p);
    boolean qIsOnLeft = covers(root.left, q);
    if (pIsOnLeft != qIsOnleft){
        return root;
    }
    TreeNode childSide = pIsOnLeft ? root.left : root.right;
    return ancestorHelper(childSide, p, q);
}

boolean covers(TreeNode root, TreeNode p){
    if (root == null) return false;
    if (root == p) return true;
    return covers(root.left, p) || covers(root.right, p);
}
```


