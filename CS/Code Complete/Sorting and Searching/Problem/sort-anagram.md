# Sort Anagram

出处

Reorder an array of strings so that anagrams appear together.

## Solution

这并不是一个完全排序的问题，而只要求分类，并且类别一定是有限个，选择用桶排序，类别数量等于桶的数量。对一般的桶排序，每一个不同的值就对应一个桶，而这里则是所有相同字母异序词(anagram)对应一个桶，因此需要找到一个哈希函数，要求是所有相同字母异序词的哈希值相同，对应同一个桶。一种做法是将字符串排序后的结果作为字符串的哈希值。

## Complexity

假定字符串平均长度为k，数量为n，那么平均复杂度为O(klogk*n)。

## Code 

```cpp
void reOrderWithAnagrams( vector<string> strs ) {
    unordered_map<string, list<string>> buckets;
    for (auto it = strs.begin(); it != strs.end(); it++) {
       string curr_str = *it;
       sort(curr_str.begin(), curr_str.end());
       buckets[curr_str].push_back(*it); // Add this string to corresponding bucket
    }
    // Post processing, reorder the strings with the bucket
    int index = 0;
    for (auto it = buckets.begin(); it != buckets.end(); ++it) {
        list<string> &anagrams = it->second;
        for (auto local_it = anagrams.begin(); local_it != anagrams.end(); ++local_it){
            strs[index] = *local_it;
            index++;
        }
    }
    return;
}
```

