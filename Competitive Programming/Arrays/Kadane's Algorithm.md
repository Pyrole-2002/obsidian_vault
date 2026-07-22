> [!tip] Given an integer array, find the contiguous subarray (containing at least one number) which has the largest sum. Return the sum and print the subarray.
- We iterate the given array and while iterating, we add the elements in a `sum` variable.
- After each update of `sum`, we compare it to the maximum value of `sum` encountered till now and update this maximum value.
- If at any point, `sum` becomes negative, we reset `sum` to $0$ and continue adding it.
```cpp
long long maxSumKadane(vector<int> arr)
{
	long long n = arr.size();
	long long max_sum = LONG_MIN; // maximum sum
	long long sum = 0;
	int temp_start, start, end;
	for (int i = 0; i < n; i++)
	{
		if (sum == 0)
			temp_start = i; // start of the subarray
		sum += arr[i];
		if (sum > max_sum)
		{
			max_sum = sum;
			start = temp_start;
			end = i;
		}
		sum = max(0ll, sum);
	}
	// Optional: print the subarray
	for (int i = start; i <= end; i++)
		cout << arr[i] << " ";
	cout << endl;
	return max_sum;
}
```
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$