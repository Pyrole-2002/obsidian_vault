- Works by repeatedly selecting the smallest (or largest) element from the unsorted portion of the list and moving it to the sorted portion of the list.
```cpp
void selectionSort(vector<int>& arr)
{
	int n = arr.size();
	for (int i = 0; i < n-1; i++)
	{
		int minIndex = i;
		for (int j = i+1; j < n; j++)
		{
			if (arr[j] < arr[minIndex])
				minIndex = j;
		}
		swap(arr[i], arr[minIndex]);
	}
}
```
- Time Complexity: $O(n^2)$
- Space Complexity: $O(1)$
- Selection sort is an in-place algorithm, as it does not require extra space.