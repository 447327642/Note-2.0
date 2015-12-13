# Zazzle

第一个给我当面面试的公司。On Campus 面试，问题不算太难，但是因为是第一次非常紧张，所以感觉表现不是特别好，希望还有下一轮。

## 层次遍历

```java
Node {
	Node l; // left child
	Node r; // right child
	int v; // value stored in the node
}

// Example tree; note the binary tree doesn't have to be complete
    8     <-- level 0 (root)
  2   3   <-- level 1
4  5    1 <-- level 2

// Examples:
PrintLevel(root, 0); -> 8
PrintLevel(root, 1); -> 2 3
PrintLevel(root, 2); -> 4 5 1
```

正常的层次遍历，然后问了空间复杂度，以及如何优化

```
// Complete this function
void PrintLevel(Node root, int level) 
{
	// root, 1
    // q    : 8(u) null(u) 2 3 null
	// level: 8(u)
	// lvCnt: 0(u) 1

	if (root == null) return;
	Queue<Node> q = new LinkedList<TreeNode>();
	q.offer(root);
    q.offer(null);
    int levelCount = 0;
    List<Integer> levelList = new ArrayList<Integer)();
	
	while(true){
		// TODO add the level count
		Node n = q.poll();
		
		if (n != null){
    	    // add the value to the level
    	    levelList.add(n.v);
        	if (n.l != null) 
        		q.offer(n.l);
        	if (n.r != null)
        		q.offer(n.r);
        } else {	
    	    // Double check the index - test
            // continue adding to the q
        	if (levelCount == level){
        	    // print out the levelList
                int len = levelList.size();
                for (int i = 0; i < len; i++){
            	    System.out.println(levelList.get(i) + “ ” );
                }
        	    break;
            } 
            levelCount++;
        	levelList = new ArrayList<Integer>();
        	if (q.isEmpty()) break;
        	q.offer(null);
        }
	}
	return;
}
```

## 最大子矩阵和

给一个 n*m 矩阵，找出和最大的子矩阵。

样例

    INPUT
    0 -2 -7  0
    9  2 -6  2
    -4  1 -4  1
    -1  8  0 -2 

    OUTPUT
    15

### 题解

首先，这个子矩阵可以是任意大小的，而且起始点也可以在任何地方，所以，要把最大子矩阵找出来，我们要考虑多种情况。

假定原始矩阵的行数为M，那么对于子矩阵，它的行数可以是1到M的任何一个数，而且，对于一个K行（K < M）的子矩阵，它的第一行可以是原始矩阵的第1行到 M - K + 1 的任意一行。

例子：
对于上面的矩阵，如果子矩阵的行数是2，那么它可以是下面几个矩阵的子矩阵：

     0 -2 -7  0
     9  2 -6  2

或者

     9  2 -6  2
    -4  1 -4  1

或者

    -4  1 -4  1
    -1  8  0 -2 

在每一种情况里（我们这里有三种），我们还要找出一个最大的子矩阵，当然，这只是一种情况的最大子矩阵（局部最大），不一定是global最大。但是，如果我们知道每一种情况的最大，要找出global最大，那就小菜一碟儿了。

在讲在一个特殊情况下求最大子矩阵之前，先讲一个事实：

假设这个最大子矩阵的维数是一维，要找出最大子矩阵, 原理与求“最大子段和问题” 是一样的。最大子段和问题的递推公式是 b[j]=max{b[j-1]+a[j], a[j]}，b[j] 指的是从0开始到j的最大子段和。

例子：

假设原始矩阵为：[9，  2， -6，  2]， 那么b[] = {9, 11, 5, 7}, 那么最大字段和为11， 如果找最大子矩阵的话，那么这个子矩阵是 [9， 2] 

求最大子段和的代码如下：

```java
public int maxSubsequence(int[] array) {
	if (array.length == 0) {
		return 0;
	}
	int max = Integer.MIN_VALUE;
	int[] maxSub = new int[array.length];
	maxSub[0] = array[0];
	
	for (int i = 1; i < array.length; i++) {
		maxSub[i] = (maxSub[i-1] > 0) ? (maxSub[i-1] + array[i]) : array[i]; 
		if (max < maxSub[i]) {
			max = maxSub[i];
		}
	}
	return max;
}
```

但是，原始矩阵可以是二维的。假设原始矩阵是一个`3*n` 的矩阵，那么它的子矩阵可以是 `1*k`, `2*k`, `3*k`，（1 <= k <= n）。 如果是`1 * K`，这里有3种情况：子矩阵在第一行，子矩阵在第二行，子矩阵在第三行。如果是 `2 * k`，这里有两种情况，子矩阵在第一、二行，子矩阵在第二、三行。如果是`3 * k`，只有一种情况。

为了能够找出最大的子矩阵，我们需要考虑所有的情况。假设这个子矩阵是 `2 * k`, 也就是说它只有两行，要找出最大子矩阵，我们要从左到右不断的遍历才能找出在这种情况下的最大子矩阵。如果我们把这两行上下相加，情况就和求“最大子段和问题” 又是一样的了。

为了找出在原始矩阵里的最大子矩阵，我们要遍历所有的子矩阵的可能情况，也就是说，我们要考虑这个子矩阵有可能只有1行，2行，。。。到n行。而在每一种情况下，我们都要把它所对应的矩阵部分上下相加才求最大子矩阵（局部）。
比如，假设子矩阵是一个`3 * k`的矩阵，而且，它的一行是原始矩阵的第二行，那么，我们就要在

     9  2 -6  2
    -4  1 -4  1
    -1  8  0 -2 

里找最大的子矩阵。

如果把它上下相加，我们就变成了 4, 11, -10,1， 从这个数列里可以看出，在这种情况下，最大子矩阵是一个`3 * 2`的矩阵，最大和是15.

为了能够在原始矩阵里很快得到从 i 行到 j 行 的上下值之和，我们这里用到了一个辅助矩阵，它是原始矩阵从上到下加下来的。

假设原始矩阵是matrix, 它每一层上下相加后得到的矩阵是total，那么我们可以通过如下代码实现：

```java
int[][] total = matrix;
for (int i = 1; i < matrix[0].length; i++) {
    for (int j = 0; j < matrix.length; j++) {
	total[i][j] += total[i-1][j];
    }
}
```

如果我们要求第 i 行到第 j 行之间上下值的和，我们可以通过total[j][k] - total[i-1][k] 得到, k 的范围从1 到 matrix[0].length - 1。

有了这些知识点，我们只需要在所有的情况下，把它们所对应的局部最大子矩阵进行比较，就可以得到全局最大的子矩阵。代码如下：

```java
public int subMaxMatrix(int[][] matrix) {
	int[][] total = matrix;
	for (int i = 1; i < matrix[0].length; i++) {
		for (int j = 0; j < matrix.length; j++) {
			total[i][j] += total[i-1][j];
		}
	}
	
	int maximum = Integer.MIN_VALUE;
	for (int i = 0; i < matrix.length; i++) {
		for (int j = i; j < matrix.length; j++) {
			//result 保存的是从 i 行 到第 j 行 所对应的矩阵上下值的和
            int[] result = new int[matrix[0].length];
			for (int f = 0; f < matrix[0].length; f++) {
				if (i == 0) {
					result[f] = total[j][f];
				} else {
					result[f] = total[j][f] - total[i - 1][f];
				}
			}
			int maximal = maxSubsequence(result);
			
			if (maximal > maximum) {
				maximum = maximal;
			}
		}
	}
	
	return maximum;
}
```

## 矩阵行走问题(On Campus)

一个 n*m 矩阵，从左上角走到右下角的所有路径，只能往右或者往下。需要说出时间复杂度。

### 题解

**思路一：数学法**

1. 使用排列组合。因为只能向右走或者向下走，在(m-1)+(n-1)次行走后，才能到达终点，也就是右下角。而在这m+n-2次行走中，有m-1次是向下，n-1次向右，所以是一个选择问题 C(m+n-2,m-1) = C(m+n-2,n-1)
2. 水平行走记作0，竖直行走记作1。每一种行走足迹可以作为一个0,1串，其中n-1个0，m-1个1。可以看做0000000000000（n-1个0）1111111111111（m-1个1）的重排列，也就是：(m+n-2)!/((m-1)!(n-1)!)

所以构造一个01串即可

**思路二：深搜**

如果矩阵有的格子可以走，有的格子不可以走，输出所有路径。(a[i][j]==1表示可以走，a[i][j]==0表示不可以走）

那么加入一个判断条件即可。

深搜的时间复杂度分析：每次递归有两个状态，一共需要递归 `m+n-2` 次。所以是 O(2^(m+n-2)) = O(2^n)

```cpp
//
//  main.cpp
//  InterviewTest
//
//  Created by WangDa on 9/29/15.
//  Copyright © 2015 DaWang. All rights reserved.
//

#include <iostream>
#include <vector>
using namespace std;

int pathCount = 0;

struct Point
{
    int x;
    int y;
    Point(int i, int j) : x(i), y(j)
    {}
};

//问题1  x y是起始点，m n是终止点 向量中坐标记录路径
void Path1(int x, int y, int m, int n, vector<Point>& vec, int len)
{
    if (x == m || y == n)
        return;
    Point p(x, y);
    vec[len++] = p;
    if (x == m - 1 && y == n - 1)
    {
        pathCount++;
        cout << "Path" << pathCount << ": ";
        for (int i = 0; i < vec.size(); ++i)
            cout << "(" << vec[i].x << ',' << vec[i].y << ") ";
        cout << endl;
    }
    else
    {
        Path1(x, y+1, m, n, vec, len);
        Path1(x+1, y, m, n, vec, len);
    }
}

//问题2
void Path2(int x, int y, int m, int n, vector<Point>& vec, int len, int safe[][4])
{
    if (x == m || y == n || safe[x][y] == 0)
        return;
    Point p(x, y);
    vec[len++] = p;
    if (x == m - 1 && y == n - 1)
    {
        for (int i = 0; i < vec.size(); ++i)
            cout << vec[i].x << ' ' << vec[i].y << endl;
    }
    else
    {
        Path2(x, y+1, m, n, vec, len, safe);
        Path2(x+1, y, m, n, vec, len, safe);
    }
}

int main(int argc, const char * argv[]) {
    int m = 3, n = 4;
    int x = 0, y = 0;
    int len = 0;
    Point p(0, 0);
    vector<Point> vec(m+n-1, p);
    Path1(x, y, m, n, vec, len);
    
    //int safe[][4] = { {1, 1, 1, 0},{0, 1, 1, 1}, {0, 0, 1, 1} };
    //Path2(x, y, m, n, vec, len, safe);
    return 0;
}

```

**Follow Up**

参考 不同的路径 和 不同的路径 II


## 最长子回文串(phone screen)

+ 难度：中等

给出一个字符串（假设长度最长为1000），求出它的最长回文子串，你可以假定只有一个满足条件的最长回文串。

样例

    给出字符串 "abcdzdcab"，它的最长回文子串为 "cdzdc"。

挑战

    O(n2) 时间复杂度的算法是可以接受的，如果你能用 O(n) 的算法那自然更好。

### 题解

这个方法其实很直观，就是从头扫描到尾部，每一个字符以它为中心向2边扩展，扩展到不能扩展为止（有不同的字符），返回以每一个字符为中心的回文，然后不断更新最大回文并返回之。

算法简单，而且复杂度为O(n^2),空间复杂度为O(1)


```java
public class Solution {
    /**
     * @param s input string
     * @return the longest palindromic substring
     */
    public String longestPalindrome(String s) {
        if (s == null) {
            return null;
        }

        String ret = null;

        int len = s.length();
        int max = 0;
        for (int i = 0; i < len; i++) {
            String s1 = getLongest(s, i, i);
            String s2 = getLongest(s, i, i + 1);

            if (s1.length() > max) {
                max = Math.max(max, s1.length());
                ret = s1;
            }

            if (s2.length() > max) {
                max = Math.max(max, s2.length());
                ret = s2;
            }
        }

        return ret;
    }

    public String getLongest(String s, int left, int right) {
        int len = s.length();
        while (left >= 0 && right < len) {
            // when i is in the center.
            if (s.charAt(left) != s.charAt(right)) {
                break;
            }

            left--;
            right++;
        }

        return s.substring(left + 1, right);
    }
}

```

DP 方法

DP 因为是二维动态规划  Time:O(n^2), Space:O(n^2)

```java
public String longestPalindrome(String s) {
        if (s == null) {
            return null;
        }
        
        String ret = null;
        
        int len = s.length();
        int max = 0;
        
        boolean[][] D = new boolean[len][len];
        
        for (int j = 0; j < len; j++) {
            for (int i = 0; i <= j; i++) {
                D[i][j] = s.charAt(i) == s.charAt(j) && (j - i <= 2 || D[i + 1][j - 1]);
                if (D[i][j]) {
                    if (j - i + 1 > max) {
                        max = j - i + 1;
                        ret = s.substring(i, j + 1);
                    }
                }
            }
        }
        
        return ret;
    }
```

解说：
状态表达式：D[i][j] 表示i,j这2个索引之间的字符串是不是回文。
递推公式： D[i][j] = if ( char i == char j) && （D[i + 1][j - 1]  ||  j - i <= 2）） 这个很好理解，跟递归是一个意思，只不过 动规的好处就是我们可以重复利用这些结果。

初始化：
D[i][i] = true;实际上也不用特别初始化，都可以套到递推公式里头。 所以主页君的代码会看起来很简单。

注意：用max记录回文长度，回文找到时，更新一下max,及结果的起始地址，结束地址。

现在重点来了，我们怎么设计这个动规才可以重复利用呢？

从这里可以看出D[i + 1][j - 1]， 我们推i,j的时候用到了i+1, j-1，其实意思就是在计算i,j时，关于同一个j-1的所有的i必须要计算过。

画图如下：
    1. 00
    2. 00 01
          11
    3. 00 01 02
          11 12
             22
    3. 00 01 02 03                       
          11 12 13
             22 23  
                33

看到上面的递推关系了吗？只要我们一列一列计算，就能够成功地利用这个动规公式。这也是动态规划的关键性设计所在。
如果你不知道如何设计，就和主页群一样，画一个图来看我们计算某值的时候，需要哪些已经有的值。如上图所示，我们需要的是i+1, j - 1，实际上就是左下角的值，这样的话我们只要一列一列计算，就能成功动态规划。
注意：一行一行计算就会失败！

所以我们的循环的设计是这样的：

    for (int j = 0; j < len; j++) 
      { for (int i = 0; i <= j; i++) {

具体请参见代码，慢慢感受一下。这个才是动规的精髓咯。



