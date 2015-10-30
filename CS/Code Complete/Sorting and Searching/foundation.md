# Sorting and Searching

## 解题策略

### 动态数据结构的维护

维护动态数据(data stream)的最大值、最小值或中位数，可以考虑使用堆。如果是动态数据求最大的k个元素，因为元素总数量不确定，不能使用quick select，这种情况下也应该用堆解决。

如果需要一个动态插入/删除的有序数据结构，那么可以使用二叉搜索树，因为它天生就是一个动态的有序数组，并且支持检索。

### 对于有序／部分有序容器的搜索

用二分查找(binary search)。

### 数据范围有限、离散

数据范围有限、离散(或存在大量重复数据，即密集数据)的排序问题，一般可以使用桶排序。对于有限位数的数据(如string, `vector<int>`, int)，可以利用基数排序进行数值序或词典序排序。

### Scalability & Memory Limits 问题

对这类问题一般采用Divide & Conquer策略，即对问题进行预处理，将问题的输入进行分割、归类(sorting)，放入相应的桶(单机上的某一块Chunk，或者分布式系统中的一台单机)，再对每个桶进行后期处理，最后合并结果。

整个过程中应该用到哈希函数: 对于Memory Limits问题，一般可以直接利用哈希函数建立对象到索引的直接映射；对Scalability问题，一般可以用哈希表来记录对象与存储该对象的机器之间的映射，在该机器上进一步做映射以获得索引。

## 常见的内排序算法

所谓的内排序是指所有的数据已经读入内存，在内存中进行排序的算法。排序过程中不需要对磁盘进行读写。同时，内排序也一般假定所有用到的辅助空间也可以直接存在于内存中。与之对应地，另一类排序称作外排序，即内存中无法保存全部数据，需要进行磁盘访问，每次读入部分数据到内存进行排序。

### Merge Sort

合并排序(Merge Sort)是一种典型的排序算法，应用“分而治之(divide and conquer)”的算法思路：将线性数据结构(如array、vector或list )分为两个部分，对两部分分别进行排序，排序完成后，再将各自排序好的两个部分合并还原成一个有序结构。由于合并排序不依赖于随机读写，因此具有很强的普适性，适用于链表等数据结构。算法的时间复杂度O(nlogn)，如果是处理数组需要额外O(n)空间，处理链表只需要O(1)空间。算法实现如下：

```
void merge_sort( int array[], int helper[], int left, int right){
    if( left >= right )
        return;

    // divide and conquer: array will be divided into left part and right part
    // both parts will be sorted by the calling merge_sort
    int mid = right - (right - left) / 2;
    merge_sort( array, helper, left, mid );
    merge_sort( array, helper, mid + 1, right);

    // now we merge two parts into one
    int helperLeft = left;
    int helperLeft = left;
    int helperRight = mid + 1;
    int curr = left;
    for(int i = left; i <= right; i++)
        helper[i] = array[i];
    while( helperLeft <= mid && helperRight <= right ){
        if( helper[helperLeft] <= helper[helperRight] )
            array[curr++] = helper[helperLeft++];
        else
            array[curr++] = helper[helperRight++];
    }

    // left part has some large elements remaining. Put them into the right side
    while( helperLeft <= mid )
        array[curr++] = helper[helperLeft++];
}
```

当递归调用merge_sort返回时，array的左右两部分已经分别由子函数排序完成，我们利用helper数组暂存array中的数值，再利用两个while循环完成合并。helper数组的左右半边就是两个排序完的队列，第一个while循环相当于比较队列头，将较小的元素取出放入array，最后使得array的左半边由小到大排序完成。第二个while循环负责扫尾，把helper左半边剩余元素复制入array中。注意，此时我们不需要对helper右半边做类似操作，因为即使右半边有剩余元素，它们也已经处于array中恰当的位置。

关于合并排序更多理论方面的讨论，请见“工具箱”。

### Quick Sort (快速排序)

快速排序(Quick Sor)是最为常用的排序算法，C++自带的排序算法的实现就是快速排序。该算法以其高效性，简洁性，被评为20世纪十大算法之一(虽然合并排序与堆排序的时间复杂度量级相同，但一般还是比快速排序慢常数倍)。快速排序的算法核心与合并排序类似，也采用“分而治之”的想法：随机选定一个元素作为轴值，利用该轴值将数组分为左右两部分，左边元素都比轴值小，右边元素都比轴值大，但它们不是完全排序的。在此基础上，分别对左右两部分分别递归调用快速排序，使得左右部分完全排序。算法的平均时间复杂度是O(nlogn)，在最坏情况下为O(n^2)，额外空间复杂度O(logn)。算法实现如下：

```
int partition( int array[], int left, int right ) {
    int pivot = array[right];
    while( left != right ){
        while( array[left] < pivot && left < right)
            left++;
        if (left < right) {
            swap( array[left], array[right--]);
        }
        while( array[right] > pivot && left < right)
            right--;
        if( left < right )
            swap( array[left++], array[right]);
    }
    //array[left] = pivot;
    return left;
}
void qSort( int array[], int left, int right ){
    if( left >=right )
        return;
    int index = partition( array, left, right);
    qSort(array, left, index - 1);
    qSort(array, index + 1, right);
}
```

partition函数先选定数组right下标所指的元素作为轴值，用pivot变量存储该元素值。然后，右移left，即从左向右扫描数组，直到发现某个元素大于轴值或者扫描完成。如果某个元素大于轴值，则将该元素与轴值交换。该操作特性在于：保证交换后轴值左侧的元素都比轴值小。再次，左移right，即从右向左扫描数组，直到发现某个元素小于轴值或者扫描完成。如果某个元素小于轴值，则将该元素与轴值交换。该操作特性在于：保证交换后轴值右侧的元素都比轴值大。重复上述过程直到left和right相遇，最终相遇的位置即为轴值所在位置。由于上述操作的特性，最终轴值左侧的元素都比轴值小，轴值右侧的元素都比轴值大。

关于快速排序的更多理论讨论请见“工具箱”。C++标准模版库提供函数sort，实现快速排序的功能: sort( iterator first, iterator last, Compare comp ); // can aslo be pointers here

### Heap Sort(堆排序)

堆排序(Heap Sort)利用了我们在Chapter 4 Trees and Graphs中提到的堆作为逻辑存储结构，将输入array变成一个最大值堆。然后，我们反复进行堆的弹出操作。回顾之前所述的弹出过程：将堆顶元素与堆末元素交换，堆的大小减一，向下移动新的堆顶以维护堆的性质。事实上，该操作等价于每次将剩余的最大元素移动到数组的最右边，重复这样的操作最终就能获得由小到大排序的数组。初次建堆的时间复杂度O(n)，删除堆顶元素并维护堆的性质需要O(logn)，这样的操作一共进行n次，故最终时间复杂度O(nlogn)。我们不需要利用额外空间，故空间复杂度O(1)。具体实现如下：

```
void heapSort(int array[], int size)  { 
    Heapify(array, size);
    for (int i = 0; i < size - 1; i++)
       popHeap(array);
}
```

Heapify和popHeap的实现参考Chapter 4 Trees and Graphs 。

### Bucket Sort (桶排序) 和 Radix Sort (基数排序)

桶排序(Bucket Sort) 和基数排序(Radix Sort)不需要进行数据之间的两两比较，但是需要事先知道数组的一些具体情况。特别地，桶排序适用于知道待排序数组大小范围的情况。其特性在于将数据根据其大小，放入合适的“桶(容器)”中，再依次从桶中取出，形成有序序列。具体实现如下：

```
void BucketSort(int array[], int n, int max)
{
    // array of length n，all records in the range of [0,max)
    int tempArray[n];
    int i;
    for (i = 0; i < n; i++)
        tempArray[i] = array[i];

    int count[max];    // buckets
    memset(count, 0, max * sizeof(int));

    for (i = 0; i < n; i++) // put elements into the buckets
        count[array[i]]++;
    for (i = 1; i < max; i++)
        count[i] = count[i-1] + count [i];  // count[i] saves the starting index (in array) of value i+1

    // for value tempArray[i], the last index should be count[tempArray[i]]-1
    for (i = n-1; i >= 0; i--)
        array[--count[tempArray[i]]] = tempArray[i];
}
```

该实现总的时间代价O(max+n)，适用于max相对n较小的情况。空间复杂度也为O(max+n)，用以记录原始数组和桶计数。

桶排序只适合max很小的情况，如果数据范围很大，可以将一个记录的值即排序码拆分为多个部分来进行比较，即使用基数排序。基数排序相当于将数据看作一个个有限进制数，按照由高位到低位(适用于字典序)，或者由低位到高位(适用于数值序)进行排序。排序具体过程如下：对于每一位，利用桶排序进行分类，在维持相对顺序的前提下进行下一步排序，直到遍历所有位。该算法复杂度为O(k*n)，k为位数(或者字符串长度)。直观上，基数排序进行了k次桶排序。具体实现如下：

```
void RadixSort(int Array[], int n, int digits, int radix)
{
    // n is the length of the array
    // digits is the number of digits
    int  *TempArray = new int[n];
    int *count = new int[radix]; // radix buckets
    int i, j, k;
    int Radix = 1; // radix modulo, used to get the ith digit of Array[j]
    // for ith digit
    for (i = 1; i <= digits; i++)  {
        for (j = 0; j < radix; j++)
            count[j] = 0;            // initialize counter
        for (j = 0; j < n; j++) {
            // put elements into buckets
            k = (Array[j] / Radix) % radix;  // get a digit
            count[k]++;
        }
        
        for (j = 1; j < radix; j++) {
            // count elements in the buckets
            count[j] = count[j-1] + count[j];
        }

        // bucket sort
        for (j = n-1; j >= 0; j--)  {
            k = (Array[j] / Radix ) % radix;
            count[k]--;
            TempArray[count[k]] = Array[j];
        }
        for (j = 0; j < n; j++) {
            // copy data back to array
            Array[j] = TempArray[j];
        }
        Radix *= radix;      // get the next digit
    }
}
```

与其他排序方式相比，桶排序和基数排序不需要交换或比较，它更像是通过逐级的分类来把元素排序好。

## 常见的外排序算法

外排序算法的核心思路在于把文件分块读到内存，在内存中对每块文件依次进行排序，最后合并排序后的各块数据，依次按顺序写回文件。相比于内排序，外排序需要进行多次磁盘读写，因此执行效率往往低于内排序，时间主要花费于磁盘读写上。我们给出外排序的算法步骤如下：

假设文件需要分成k块读入，需要从小到大进行排序

1. 依次读入每个文件块，在内存中对当前文件块进行排序(应用恰当的内排序算法)。此时，每块文件相当于一个由小到大排列的有序队列
2. 在内存中建立一个最小值堆，读入每块文件的队列头
3. 弹出堆顶元素，如果元素来自第i块，则从第i块文件中补充一个元素到最小值堆。弹出的元素暂存至临时数组
4. 当临时数组存满时，将数组写至磁盘，并清空数组内容。
5. 重复过程3)，4)，直至所有文件块读取完毕

## 快速选择算法 (quick selection algorithm)

快速选择算法能够在平均O(n)时间内从一个无序数组中返回第k大的元素。算法实际上利用了快速排序的思想，将数组依照一个轴值分割成两个部分，左边元素都比轴值小，右边元素都比轴值大。由于轴值下标已知，则可以判断所求元素落在数组的哪一部分，并在那一部分继续进行上述操作，直至找到该元素。与快排不同，由于快速选择算法只在乎所求元素所在的那一部分，所以时间复杂度是O(n)。关于算法复杂度的理论分析请见“工具箱”给出的参考资料。我们给出算法实现如下：

```
int partition( int array[], int left, int right ) {
    int pivot = array[right];
    while( left != right ){
        while( array[left] < pivot && left < right)
            left++;
            left++;
        if (left < right) {
            swap( array[left], array[right--]);
        }
        while( array[right] > pivot && left < right)
            right--;
        if( left < right )
            swap( array[left++], array[right]);
    }
    return left;
}

int quick_select(int array[], int left, int right, int k)
{
    if ( left >= right )
        return array[left];
    int index = partition(array, left, right);
    int size = index - left + 1;
    if ( size == k )
        return array[left + k - 1]; // the pivot is the kth largest element
    else if ( size > k )
        return quick_select(array, left, index - 1, k);
    else
        return quick_select(array, index + 1, right , k - size);
}
```

> Get the k largest elements in an array with O(n) expected time, they don’t need to be sorted.

解题分析：实际上和quick select的应用场景是一致的，先找到第k大的元素，再将数组重新整理，找出比第k大的元素小的所有元素。

> There are n points on a 2D plan, find the k points that are closest to origin ( x= 0, y= 0).

解题分析：在这里已知点的数量，因此k个点到原点的距离构成size确定的静态数组，应该对这个数组使用快速选择算法。

## 二分查找 (Binary search)

对于已排序的有序线性容器而言(比如数组，vector)，二分查找(Binary search)几乎总是最优的搜索方案。二分查找将容器等分为两部分，再根据中间节点与待搜索数据的相对大小关系，进一步搜索其中某一部分。二分查找的算法复杂度为O(logn)，算法复杂度的具体分析请见“工具箱”给出的参考资料。算法实现如下：

```
int binarySearch(int *array, int left, int right, int value) {
    if (left > right) {
        // value not found
        return -1;
    }

    int mid = right - (right - left) / 2;
    if (array[mid] == value) {
        return mid;
    } else if (array[mid] < value) {
        return binarySearch(array, mid + 1, right, value);
    } else {
        return binarySearch(array, left, mid - 1, value);
    }
}
```

对于局部有序的数据，也可以根据其局部有序的特性，尽可能地利用逼近、剪枝，使用二分查找的变种进行搜索。


