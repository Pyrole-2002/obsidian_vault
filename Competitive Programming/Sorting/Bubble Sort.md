- Works by repeatedly swapping the adjacent elements if they are in the wrong order.
```cpp
void bubbleSort(vector<int>& arr)
{
	int n = arr.size();
	for (int i = 0; i < n - 1; i++)
	{
		bool swapped = false;
		for (int j = 0; j < n - i - 1; j++)
		{
			if (arr[j] > arr[j + 1])
			{
				swap(arr[j], arr[j + 1]);
				swapped = true;
			}
		}
		if (!swapped)
			break;
	}
}
```
- Time Complexity: $O(n^2)$
- Space Complexity: $O(1)$
- Selection sort is an in-place algorithm, as it does not require extra space.