# Merge Range

给出若干闭合区间，合并所有重叠的部分。

样例

    给出的区间列表 => 合并后的区间列表：
    [                     [
      [1, 3],               [1, 6],
      [2, 6],      =>       [8, 10],
      [8, 10],              [15, 18]
      [15, 18]            ]
    ]

挑战

    O(n log n) 的时间和 O(1) 的额外空间。

## Solution

To check the intersections between interval [a,b] and [c,d],  there are four cases (equal not shown in the figures):

        a____b
    c____d

    a____b
         c____d

    a_______b
        c___d

       a___b
    c_______d

But we can simplify these into 2 cases when check the smaller (smaller start point) interval with the bigger interval.

For the problem, the idea is simple. First sort the vector according to the start value. Second, scan every interval, if it can be merged to the previous one, then merge them, else push it into the result vector.

## Complexity

时间复杂度 O(n)，空间复杂度 O(1)

## Code

```python

"""
Definition of Interval.
class Interval(object):
    def __init__(self, start, end):
        self.start = start
        self.end = end
"""

class Solution:
    # @param intervals, a list of Interval
    # @return a list of Interval
    def merge(self, intervals):
        res = []    # result list

        if len(intervals)==0:
            return res

        #sort list according to the start value
        intervals.sort(key=lambda x:x.start)

        res.append(intervals[0])

        #scan the list
        for i in xrange(1,len(intervals)):
            cur = intervals[i]
            pre = res[-1]
            #check if current interval intersects with previous one
            if cur.start <= pre.end:
                res[-1].end = max(pre.end, cur.end) #merge
            else:
                res.append(cur) #insert

        return res
```

