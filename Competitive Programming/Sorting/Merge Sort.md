- Follows the divide-and-conquer approach.
- Works by recursively dividing the input array into smaller subarrays and sorting those subarrays, then merging them back together to obtain the sorted array.
```cpp
void mergeSort(vector<int>& arr, int l, int r)
{
	if (l >= r)
		return;
	int m = l + (r - l) / 2;
	mergeSort(arr, l, m);
	mergeSort(arr, m + 1, r);

	// merge
	vector<int> temp(r - l + 1);
	int i = l, j = m + 1, k = 0;
	while (i <= m && j <= r)
	{
		if (arr[i] <= arr[j])
			temp[k++] = arr[i++];
		else
			temp[k++] = arr[j++];
	}
	while (i <= m)
		temp[k++] = arr[i++];
	while (j <= r)
		temp[k++] = arr[j++];
	for (int i = l; i <= r; i++)
		arr[i] = temp[i - l];
}
```
- Time Complexity: $O(n\log n)$
- Space Complexity: $O(n)$
- Merge sort is not an in-place sorting algorithm.