# All Permutation 

出处

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

## Solution

选择后驱元素中的一个与当前节点交换，然后再将后面的节点作为子问题考虑。由于有重复元素，而重复元素对当前节点的影响是相同的，因此应该去重：把相同元素的替换作为同一个回溯方向/选择来处理，一旦发现是已经处理过的相同元素，则直接跳过。

## Complexity

时间复杂度 O(n^2 )，空间复杂度 O(n^2 )

## Code 

```java
void helper(int index, ArrayList<Integer> path, ArrayList<ArrayList<Integer>> result){
	if (index > path.size()) return;
	
	if (index == path.size())
		result.add(new ArrayList<Integer>(path);
	
	HashSet<Integer> used = new HashSet<Integer>();
	for (int i = index; i < path.size(); i++){
		// handle duplicate
		if (used.contains(path.get(i));
			continue;
			
		// make choice
		swap(path, index, i);
		helper(index+1, path, result);
		// backtracing
		swap(path, index, i);
		used.put(path.get(i));
	}
}

ArrayList<ArrayList<Integer>> allPermutation(ArrayList<Integer> num){
	ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();
	helper(0, num, result);
	return result;
}
```

