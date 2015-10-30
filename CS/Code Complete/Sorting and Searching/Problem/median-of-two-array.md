# Median of Two Sorted Array

出处

## Solution

对于含有n个数的数组，若n为奇数，则中位数array[n/2+1]，若n为偶数，则中位数为 (array[n/2] + array[n/2+1]) / 2。所以我们当前的目标是设计一种算法能够返回第k大的元素。对于查找第k大的数，最先想到的应该是快速选择算法，但是该算法并没有利用数组已经有序的特性，故时间复杂度O(m+n)。与二分查找相似，快速选择算法的核心在于快速缩小搜索范围。

对于本例，我们应该如何缩小搜索范围呢？假设数组分别记为A，B。当前需要搜索第k大的数，于是我们可以考虑从数组A中取出前m个元素，从数组B中取出k-m个元素。由于数组A，B分别排序，则A[m]大于从数组A中取出的其他所有元素，B[k-m] 大于数组B中取出的其他所有元素。此时，尽管取出元素之间的相对大小关系不确定，但A[m]与B[k-m]的较大者一定是这k个元素中最大的。那么，较小的那个元素一定不是第k大的，它至多是第k-1大的：因为它小于其他未被取出的所有元素，并且小于取出的k个元素中最大的那个。为叙述方便，假设A[m]是较小的那个元素。那么，我们可以进一步说，A[1], A[2]…A[m-1]也一定不是第k大的元素，因为它们小于A[m]，而A[m]至多是第k-1大的。因此，我们可以把较小元素所在数组中选出的所有元素统统排除，并且相应地减少k值。这样，我们就完成了一次范围缩小。特别地，我们可以选取m=k/2。

## Complexity

每次缩小范围之后k值基本上折半，故时间复杂度O(logn)。

## Code 

```cpp
double helper(int A[], int m, int B[], int n, int k){
    // find the kth largest element
    if(m > n)
        return helper(B, n, A, m, k);//make sure that the second one is the bigger array;
    if(m == 0)
        return B[k - 1];
    if(k == 1){
        return min(A[0], B[0]);
    }
    int pa = min(k / 2, m); // assign k / 2 to each of the array and cut the smaller one
    int pb = k - pa;
    if (A[pa-1] <= B[pb-1])
        return helper(A + pa, m - pa, B, n, k - pa);
    return helper(A, m, B + pb, n - pb, k - pb);
}

double findMedianSortedArrays(int A[], int m, int B[], int n) {
    int total = m + n;
    if(total % 2 == 0){
        return (helper(A, m, B, n, total / 2) + helper(A, m, B, n, total / 2 + 1)) / 2;
    }
    return helper(A, m, B, n, total / 2 + 1);
}
```

