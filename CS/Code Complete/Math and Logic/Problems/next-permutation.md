# Next Permutation

Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be in-place, do not allocate extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.

    1,2,3 → 1,3,2
    3,2,1 → 1,2,3
    1,1,5 → 1,5,1
    
## Solution

Take A = {1,3,2} as an example:

1. Traverse from back to forth, find the turning point, that is A[i] = 3.
2. Sort from the turning point to the end (A[i] to A[end]), so {3,2} becomes {2,3}.
3. If i equals to 0, finish! Else, goto 4.
4. Let j = i, search from A[j] to A[end] to find the first elem which is larger than A[i-1], '2' here.
5. Swap the elem A[j] with A[i-1].

Finally, the next permutation is {2,1,3}.

### Complexity

时间复杂度 O(n)，空间复杂度 O(1)

## Code

```java
public class Solution {
    public void nextPermutation(int[] num) {
        int last = num.length - 1;
        int i = last;
        while (i >0 && num[i - 1] >= num [i]) --i;
        if (i == 0) {
            for (int l = 0, r = last; l < r; ++l, --r) {
                int tmp = num[l];
                num[l] = num[r];
                num[r] = tmp;
            }
            return;
        }
        for (int j = last; j >= i; --j) {
            if (num[j] > num[i-1]) {
                int tmp = num[j];
                num[j] = num[i-1];
                num[i-1] = tmp;
                for (int l = i, r = last; l < r; ++l, --r) {
                    int t = num[l];
                    num[l] = num[r];
                    num[r] = t;
                }
                return;
            }
        }
    }
}
```

