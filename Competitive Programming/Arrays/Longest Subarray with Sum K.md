## Positives Only
> [!tip] Given an array with only non-negative values and a sum $k$, we need to return the length of the longest subarray that sums to $k$.
### Method 1 (Hashing)
- This approach uses a hash map to efficiently find the longest subarray with a sum of $k$.
- The hash map `pre_sum_map` will store prefix sums and their corresponding indices.
- Assume the sum of a subarray starting from index $0$ and ending at index $i$ is `sum` $(> k)$. In this subarray, we search for another subarray starting at non-zero index and ending at index $i$ whose sum equals $k$. This means that the sum of the new subarray should be `sum - k`.
- **Edge Case:** We need to check the map if the prefix sum already exists and if it does, we do not update the index as we want the index to be as minimum as possible so we consider the earliest index where `sum - k` occurred.
```cpp
int getLongestSubarray(vector<int> a, long long k)
{
	int n = a.size();
	map<long long, int> pre_sum_map;
	long long sum = 0;
	int max_len = 0;
	for (int i = 0; i < n; i++)
	{
		// calculate prefix sum till index i
		sum += a[i];
		// if prefix sum is k, update max_len
		if (sum == k)
			max_len = max(max_len, i + 1);
		// if prefix sum is not k, check if (sum - k) is present in map
		long long rem = sum - k;
		// if (sum - k) is present in map, update max_len
		if (pre_sum_map.find(rem) != pre_sum_map.end())
			max_len = max(max_len, i - pre_sum_map[rem]);
		// Edge Case: if sum is not present in map, add it to map
		if (pre_sum_map.find(sum) == pre_sum_map.end())
			pre_sum_map[sum] = i;
	}
	return max_len;
}
```
- Time Complexity: If we are using an [[Unordered Map]], the time complexity will be $O(N)$ but in worst case the complexity becomes $O(N^2)$. If we use [[Map]], the time complexity will be $O(N\log N)$.
- Space Complexity: $O(N)$
### Method 2 (2 Pointers)
- We take two pointers `left` and `right`, initially pointing to the index $0$.
- The `left` will denote the starting index of the subarray and the `right` will denote the ending index (both inclusive).
- Since we want the longest subarray, we move the `right` pointer forward every time adding the element to the overall sum.
- As soon as the sum of the subarray crosses $k$, we need to move the `left` pointer forward to shrink the size and therefore decrease the sum.
- We consider the length of the subarray whenever the sum becomes equal to $k$.
```cpp
int getLongestSubarray(vector<int> a, long long k)
{
	int n = a.size();
	int left = 0, right = 0; // two pointers
	long long sum = 0;
	int max_len = 0;
	while (right < n)
	{
		sum += a[right];
		while (sum > k && left <= right)
		{
			sum -= a[left];
			left++;
		}
		if (sum == k)
			max_len = max(max_len, right - left + 1);
		// move right pointer forward
		right++;
	}
	return max_len;
}
```
- Time Complexity: $O(2\times N)$
- Space Complexity: $O(1)$
---
## Positives & Negatives
> [!tip] Given an array with both positive and negative integers and a sum $k$, we need to return the length of the longest subarray that sums to $k$.
- We again use hash map to store the prefix sums and corresponding indices.
- The algorithm and code is exactly same as [[Longest Subarray with Sum K#Method 1 (Hashing)|Method 1 (Hashing)]].
---
## $k = 0$
> [!tip] Given an array containing both positive and negative integers, find the length of the longest subarray that has sum equal to $0$.
- We store the prefix sum of every element, and if prefix sums at 2 different elements become same, the subarray between the elements will have zero sum.
```cpp
int getLongestSubarrayZero(vector<int> a)\
{
	int n = a.size();
	unordered_map<int, int> mp; // sum, index
	int max_len = 0, sum = 0;
	for (int i = 0; i < n; i++)
	{
		sum += a[i];
		if (sum == 0)
			max_len = i + 1;
		else
		{
			if (mp.find(sum) != mp.end())
				max_len = max(max_len, i - mp[sum]);
			else
				mp[sum] = i;
		}
	}
	return max_len;
}
```
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$