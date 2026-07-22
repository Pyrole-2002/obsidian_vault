- Works by iteratively inserting each element of an unsorted list into its correct position in a sorted portion of the list.
```cpp
void insertionSort(vector<int>& arr)
{
	int n = arr.size();
	for (int i = 1; i < n; i++)
	{
		int key = arr[i];
		int j = i - 1;
		while (j >= 0 && arr[j] > key)
		{
			arr[j + 1] = arr[j];
			j--;
		}
		arr[j + 1] = key;
	}
}
```
- Time Complexity: $O(n^2)$
- Space Complexity: $O(1)$
- Selection sort is an in-place algorithm, as it does not require extra space.