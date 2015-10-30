# Edit Distance

给出两个单词word1和word2，计算出将word1 转换为word2的最少操作次数。

你总共三种操作方法：

+ 插入一个字符
+ 删除一个字符
+ 替换一个字符

样例

    给出 work1="mart" 和 work2="karma"
    返回 3

## Solution

res[i][j]表示Edit Distance between X数组的前i个元素以及Y数组的前j个元素，或者the minimum # of operations to convert X前i个元素 into Y的前j个元素

因为对于Xi 和 Yj，操作无非是 insert, delete, replace三种，所以递归式就是三项：根据上面这个图很清楚：res[i][j] = min{res[i-1][j]+1, res[i][j-1]+1, Xi == Yj ? res[i-1][j-1] : res[i-1][j-1] + 1}

## Complexity

时间复杂度 O(n^2 ) 空间复杂度 O(n^2 )，空间复杂度可以优化至 O(n)

## Code

```java
public class Solution {
    /**
     * @param word1 & word2: Two string.
     * @return: The minimum number of steps.
     */
    public int minDistance(String word1, String word2) {
        if (word1==null && word2!=null) return word2.length();
        if (word1!=null && word2==null) return word1.length();
        if (word1==null && word2==null) return 0;
        int[][] res = new int[word1.length()+1][word2.length()+1];
        for (int i=1; i<=word1.length(); i++) {
            res[i][0] = i;
        }
        for (int j=1; j<=word2.length(); j++) {
            res[0][j] = j;
        }
        for (int m=1; m<=word1.length(); m++) {
            for (int n=1; n<=word2.length(); n++) {
                res[m][n] = Math.min(Math.min(res[m-1][n]+1, res[m][n-1]+1), word1.charAt(m-1)==word2.charAt(n-1)? res[m-1][n-1] : res[m-1][n-1]+1);
            }
        }
        return res[word1.length()][word2.length()];
    }
}

```

