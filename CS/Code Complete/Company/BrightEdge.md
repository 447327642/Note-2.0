# BrightEdge

小公司，题目重复率也比较高，基本都是印度人面。我的体验是第一轮  behavioral电面，然后就发一个48小时coding assignment，每个人的具体内容略不相同，大体上是用 java实现一个网络爬虫。

面试官：Hongbo Ma, Senior Software Engineer, 

questions about computer science fundamentals like data structures, algorithms and OOP.

下面是具体的面试题准备

## Fibonacci 数列

### 递归版本

```
int fibonacciRecursive(int n){
    if (n < 2)
        return n;
    
    return f(n-1) + f(n-2);
}
```
时间复杂度 O(2^n) 空间复杂度 O(n)

### 迭代版本

```
int fibonacci(int n){
    if (n < 2)
        return n;
    
    int a = 0;
    int b = 1;
    int c = -1;
    for (int i = 2; i < n; i++){
        c = a + b;
        a = b;
        b = c;
    }
    return c;
}

```

时间复杂度 O(n) 空间复杂度 O(1)


## 如何判断一个BST是否valid。

也就是判断一个Binary Tree是不是Binary Search Tree

1. Add lower & upper bound. O(n)
2. Inorder traversal with one additional parameter (value of predecessor). O(n)

```java
public class Solution {
    boolean isValidBSTRe(TreeNode root, long left, long right)
    {
        if(root == null) return true;
        return left < root.val && root.val < right &&
                isValidBSTRe(root.left,left,root.val) 
                && isValidBSTRe(root.right, root.val, right);
    }
    public boolean isValidBST_1(TreeNode root) {
        if (root == null) return true;
        return isValidBSTRe(root, (long)Integer.MIN_VALUE - 1, (long)Integer.MAX_VALUE + 1);
    }
    
    boolean isValidBST(TreeNode root) {
        long[] val = new long[1];
        val[0] = (long)Integer.MIN_VALUE - 1;
        return inorder(root, val);
    }
    
    boolean inorder(TreeNode root, long[] val) {
        if (root == null) return true;
        if (inorder(root.left, val) == false) 
            return false;
        if (root.val <= val[0]) return false;
        val[0] = root.val;
        return inorder(root.right, val);
    }
}

```

用中序遍历的方法遍历BST，BST的性质是遍历后数组是有序的。根据这一点我们只需要中序遍历这棵树，然后保存前驱结点，每次检测是否满足递增关系即可。注意以下代码我么用一个一个变量的数组去保存前驱结点，原因是java没有传引用的概念，如果传入一个变量，它是按值传递的，所以是一个备份的变量，改变它的值并不能影响它在函数外部的值，算是java中的一个小细节。

```java
public class Solution {
    /**
     * @param root: The root of binary tree.
     * @return: True if the binary tree is BST, or false
     */
    public boolean isValidBST(TreeNode root) {
        ArrayList<Integer> pre = new ArrayList<Integer>();
        pre.add(null);
        return helper(root, pre);
    }
    private boolean helper(TreeNode root, ArrayList<Integer> pre)
    {
        if(root == null)
            return true;
        boolean left = helper(root.left,pre);
        if(pre.get(0)!=null && root.val<=pre.get(0))
            return false;
        pre.set(0,root.val);
        return left && helper(root.right,pre);
    }
}
```

## String permutation

lintcode: 带重复元素的排列

cc: Permutations with / without Dups

### 无重复元素的情况

P(a1) = a1

P(a1a2) = a1a2, a2a1

P(a1a2a3) = a1a2a3, a1a3a2, a2a1a3, a2a3a1, a3a1a2, a3a2a1

从第二步到第三步，恩可以看作是，对第二步中的两个结果，分别把 a3 插入到的每个结果中可能的各个位置，于是我们可以根据这个规律写出代码

```java
public static ArrayList<String> getPermutations(String str){
    if (str == null){
        return null;
    }

    ArrayList<String> permutations = new ArrayList<String>();
    if (str.length() == 0){
        permutations.add("");
        return permutations;
    }

    char first = str.charAt(0);
    String rest = str.substring(1);
    ArrayList<String> words = getPermutations(rest);
    for (String word : words){
        for (int j = 0; j <= word.length(); j++){
            permutations.add(word.substring(0,j) + first + word.substring(j));
        }
    }
    return permutations;
}
```

### 有重复元素的情况

We would like to only create the unique permuatations, rather than creating every permutation and then ruling out the duplicates

We can start with computing the count of each letter(hash table) for a string such as aabbbbc, we have a(2) b(4) c(1).

The first choice we make is whether to use an a, b or c as the first character. After that, we have a subp;roblem to solve: find all permutations of the remaining characters, and append those to the already picked "prefix"

P(a2|b4|c1) = {a + P(a1|b4|c1)} + {b + P(a2|b3|c1)} + {c + P(a2|b4|c0)} 依次递推

```java

ArrayList<String> get Perms(String s){
    ArrayList<String> result = new ArrayList<String>();
    HashMap<Character, Integer> map = buildFreqTable(s);
    getPerms(map, "", s.length(), result);
    return result;
}

HashMap<Character, Integer> buildFreqTable(String s){
    ArrayList<String> map = new HashMap<Character, Integer>();
    for (char c : s.toCharArray()) {
        if (!map.containsKey(c))
            map.put(c, 0);
            
        map.put(c, map.get(c) + 1;
    }
    return map;
}

void getPerms(HashMap<Character, Integer> map, String prefix, int remaining, ArrayList<String> result) {
    if (remaining == 0){
        result.add(prefix);
        return;
    }
    
    for (Character c : map.keySet()) {
        int count = map.get(c);
        if (count > 0) {
            map.put(c, count - 1);
            getPerms(map, prefix + c, remaining - 1, result);
            map.put(c, count);
        }
    }
}

```

## reverse linkedlist

```java
public class Solution {

    // recursive
    public ListNode reverseList(ListNode head) {
        if(head == null) return null;
        if(head.next == null) return head;

        ListNode tail = head.next;
        ListNode reversed = reverseList(head.next);

        tail.next = head;

        head.next = null;

        return reversed;
    }

    // iterative
    public ListNode reverseList_2(ListNode head) {
        if(head==null || head.next == null) 
            return head;

        ListNode p1 = head;
        ListNode p2 = head.next;

        head.next = null;
        while(p1!= null && p2!= null){
            ListNode t = p2.next;
            p2.next = p1;
            p1 = p2;
            if (t!=null){
                p2 = t;
            }else{
                break;
            }
        }

        return p2;
    }
}
```

## all characters combinations 

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

For example,

If n = 4 and k = 2, a solution is:

```
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

DFS + 回溯

```java

public class Solution {
     public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        ArrayList<Integer> path = new ArrayList<Integer>();
        combineRe(n, k, 1, path, res);
        return res;
    }

    void combineRe(int n, int k, int start, ArrayList<Integer> path, List<List<Integer>> res){
        int m = path.size();
        if (m == k) {
            ArrayList<Integer> p = new ArrayList<Integer>(path);
            res.add(p);
            return;
        }
        for (int i = start; i <= n-(k-m)+1; ++i) {
            path.add(i);
            combineRe(n,k,i+1, path, res);
            path.remove(path.size() - 1);
        }
    }
}

```


## Lowest Common Ancestor

要求O(1) 空间

```java
TreeNode commonAncestor(TreeNode p, TreeNode q){
    if (p == q) return null;

    TreeNOde ancestor = p;
    while (ancestor != null){
        if (isOnPath(ancestor, q)){
            return ancestor;
        }
        ancestor = ancestor.parent;
    }
    return null;
}

boolean isOnPath(TreeNode ancestor, TreeNode node){
    while (node != ancestor && node != null){
        node = node.parent;
    }
    return node == ancestor;
}
```

isOnPath method will take O(dq) time, where dq is the depth of d. Runtime O(dp * dq)

On a balanced tree, dp and dq are O(log N), worst case O(N^2)

### Without link to parent

```java
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

## Mirror a binary tree

```java
public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if(root!=null){
            helper(root);
        }

        return root;    
    }

    public void helper(TreeNode p){

        TreeNode temp = p.left;
        p.left = p.right;
        p.right = temp;

        if(p.left!=null)
            helper(p.left);

        if(p.right!=null)
            helper(p.right);
    }

    public TreeNode invertTree_2(TreeNode root) {
        LinkedList<TreeNode> queue = new LinkedList<TreeNode>();

        if(root!=null){
            queue.add(root);
        }

        while(!queue.isEmpty()){
            TreeNode p = queue.poll();
            if(p.left!=null)
                queue.add(p.left);
            if(p.right!=null)
                queue.add(p.right);

            TreeNode temp = p.left;
            p.left = p.right;
            p.right = temp;
        }

        return root;    
    }
}
```

## Pow(double a, int b)

recursive and think about the edge condition

```java
public class Solution {
    public double myPow(double x, int n) {
        if (x < 0) return (n % 2 == 0) ? myPow(-x, n) : -myPow(-x, n);
        if (x == 0 || x == 1) return x;
        if (n < 0) return 1.0 / myPow(x,-n);
        if (n == 0) return 1.0;
        if (n == 1) return x;
        double half = myPow(x,n/2);
        if (n % 2 == 0) return half * half;
        else return x * half * half;
    }
}
```

## Partition array

1. input: [1, 2, 3, 6, 0, 0, 3, 1, 9, 0]
2. output: [1, 2, 3, 6, 3, 1, 9, 0, 0, 0]


就是保持顺序的同时把 0 都移到最后面去

维护两个值，一个是零出现的位置，一个是零之后第一个不为 0 的位置，然后交换。交换之后各加 1，然后再检查，直到找不到不是0的为止。 这个是 O(n^2)

有更好的解法...然而我做出来就没时间了

leetcode 265 代码如下

```
public class Solution {
    public void moveZeroes(int[] nums) {
        int i = -1, j = 0;
        while (j < nums.length) {
            if (nums[j] != 0) {
                swap(++i, j, nums);
            }
            j++;
        }
    }

    public void swap(int i, int j, int[] nums) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

下面是面试代码，坑了坑了坑了

```
// test 0 1 2 3 4
// test 1 0 3 0 7  iz = 1 inz = 2  i = 2
// test 1 3 0 0 7  iz = 2 inz = 4 i = 4
// test 1 3 7 0 0 i = 3 inz = -1
// worst case  0000011111
// 1000001111


int[] move0totail2(int[] input){
	if (input.length == 0){
	    return null;
    }

    int inonzero = 0;
    int zerocount = 0;
    for (int i = 0; i < input.length; i++){
	    if (input[i] == 0){
	        zerocount++;
        } else {
	        input[inonzero] = input[i];
	        inonzero++;
        }
    }
    for (int i = 1; i <= zerocount; i++){
	    input[input.length - i] = 0;
    }
    return input;
}

// input: int[]
// output: int[]
int[] move0totail(int[] input){
	if (input.length == 0){
	    return null;
    }
    // two pointer: izero, inonzero
    int izero = -1;
    int inonzero = -1;
    for (int i = 0; i < input.length; i++){
	    if (input[i] == 0){
		    if (izero != -1)
                izero = i;  // find first zero
        } else {
	        if (izero != -1){
	            inonzero = i; // find first nonzero if have zero
            }
        }

        if (izero != -1 && inonzero != -1){
    	    // swap the element izero and inonzero
    	    swap(input, izero, inonzero);
    	    izero = -1;
    	    inonzero = -1;
    	    i = izero + 1;
        }
    }
    return input[];
}

void swap(int[] arr, int i, int j){
	int temp = arr[i];
	arr[i] = arr[j];
	arr[j] = temp;
}
```



## 设计一个parking lot

这是一个非常常见的设计题目，stackoverflow有一个帖子对这个题目描述的非常好，可以参考一下。

+ multiple floors
+ each floor has multiple parking spots
+ parking spots has two types. handicapped or regular parking.
+ Question: how many regular parking are available at 2nd floor?

Brief Design

+ enum VehicleSize {Moto, Compact, Large}
+ abstract class Vehicle
    + protected ParkingSpot parkingSpot
    + protected VehicleSize size
    + public parkInSpot(ParkingSpot s) {parkingSpot = s; }
    + public clearSpot() {...}
+ class Bus extends Vehicle
+ class Car extends Vehicle
+ class Moto extends Vehicle
+ class ParkingLot
    + private Level[] levels
    + private final int NUM_LEVELS = 5;
    + public boolean parkVehicle(Vehicle vehicle) {...}
+ class Level
    + private int floor;
    + private ParkingSpot[] spots;
    + private int available Spots = 0;
    + public boolean parkVehicle(Vehicle vehicle) {...}
    + public void spotFreed() { availableSpots++; }
+ class ParkingSpot
    + private Vehicle vehicle;
    + private VehicleSize spotSize;
    + private int number;
    + private Level level;
    + public boolean isAvailable() { return vehicel == null; }
    + public boolean park(Vehicle v) {...}
    + public remove Vehicle() {...}


## 测试题

+ 给你一个ATM Function to withdraw money,问如何测试
+ 给你一个website，问你如何测试。

