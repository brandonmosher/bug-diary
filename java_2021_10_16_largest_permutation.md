# Explanation

This function returns the largest possible lexicographical permutation of arr containing n distincts integers after k swaps. My solution uses a greedy algorithm which iterates through arr, swapping each element with the largest unvisited element. As an optimization, I use an sorted set to select the index of largest unvisited element. This set contains the indices of arr ordered by the corresponding values of arr using a custom comparator. After each swap, the index that previously pointed to the largest element, points to a different element. Therefore, this index must be removed and re-added to the sorted set to ensure it is properly ordered.

In the first version of the function below, I remove and add max_index to the sorted set after completing the swap. This would work if, as I initially assumed, the set used the provided comparator to maintain order, and the equals() function to match keys. Set in Java, however, uses the comparator for both purposes. Therefore, removing and re-adding the max_index after the swap has no effect because the key max_index now matches the key i (because the value previously at arr[max_index] is now at arr[i]), but i has been removed from the set. Similarly, when max_index is re-added to the set, no matching key is found which results in the set containing max_index twice.

The correct ordering, as in the second version below, is to remove max_index, swap, and then re-add max_index. This ensures that both i and max_index are removed from the set (at this point the key i matches the key i and the key max_index matches max_index). Finally, max_index, which now corresponds to the value previously at arr[i], is re-added in the proper order.

# Buggy
```
public static List<Integer> largestPermutation(int k, List<Integer> arr) {
    SortedSet<Integer> m = new TreeSet<Integer>((a,b) -> arr.get(a).compareTo(arr.get(b)));
    for(int i = 0; i < arr.size(); ++i) { m.add(i); }
    int i = 0;
    while(k > 0 && !m.isEmpty()) {
        int max_index = m.last();
        m.remove(i);
        if(i != max_index) {
            Collections.swap(arr, i, max_index);
            m.remove(max_index);
            m.add(max_index);
            --k;
        }
        ++i;
    }
    return arr;
}
```

# Less Buggy
```
public static List<Integer> largestPermutation(int k, List<Integer> arr) {
    SortedSet<Integer> m = new TreeSet<Integer>((a,b) -> arr.get(a).compareTo(arr.get(b)));
    for(int i = 0; i < arr.size(); ++i) { m.add(i); }
    int i = 0;
    while(k > 0 && !m.isEmpty()) {
        int max_index = m.last();
        m.remove(i);
        if(i != max_index) {
            m.remove(max_index);
            Collections.swap(arr, i, max_index);
            m.add(max_index);
            --k;
        }
        ++i;
    }
    return arr;
}
```