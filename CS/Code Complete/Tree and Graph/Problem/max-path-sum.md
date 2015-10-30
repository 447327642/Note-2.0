# Maximum Path Sum

出处

Given a binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path does not need to go through the root.

For example:

Given the below binary tree,

       1
      / \
     2   3

Return 6.

## Solution

Recursion

## Complexity

这个暂时不知道怎么算。。

## Code

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public int maxPathSum(TreeNode root) {
        int[] res = new int[1];
        res[0] = Integer.MIN_VALUE;
        maxPathSumRe(root, res);
        return res[0];
    }
    int maxPathSumRe(TreeNode root, int[] res) {
        if (root == null) return 0;
        int left = maxPathSumRe(root.left, res);
        int right = maxPathSumRe(root.right, res);
        res[0] = Math.max(res[0], root.val + Math.max(left, 0) + Math.max(right, 0));
        return Math.max(root.val, root.val + Math.max(left, right));
    }
}
```

