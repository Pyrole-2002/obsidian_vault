- Based on the divide-and-conquer algorithm that picks an element as a pivot and partitions the given array around the picked pivot by placing the pivot in its correct position in the sorted array.
- During partition, the pivot is placed in its correct position in the sorted array by putting all smaller elements to the left of the pivot, and all greater elements to the right of the pivot.
- There are many different choices for picking pivots which affects the performance of quick sort.
```cpp
int partition(vector<int>& arr, int l, int r)
{
	int pivot = arr[r]; // can be any element
	int i = l - 1;

	for (int j = l; j < r; j++)
	{
		if (arr[j] < pivot)
		{
			i++;
			swap(arr[i], arr[j]);
		}
	}

	swap(arr[i + 1], arr[r]);
	return i + 1;
}

void quickSort(vector<int>& arr, int l, int r)
{
	if (l < r)
	{
		int pi = partition(arr, l, r);

		quickSort(arr, l, pi - 1);
		quickSort(arr, pi + 1, r);
	}
}
```
- Time Complexity: $O(n^2)$ in worst case but $O(n\log n)$ for average case.
- Space Complexity: $O(n)$ due to the recursive call stack.
- Quick sort is an in-place algorithm, as it does not require extra space.