> [!tip] Given an array of $N$ integers, write an algorithm to return the element that occurs more than $\frac{N}{2}$ times in the given array.
- We keep a note of two variables `count` and `element`.
- `element` is the value that we are expecting to be the answer.
- `count` is for tracking the count of `element`.
- We start iterating through the array:
	- If `count` is $0$, store the current element of the array as `element`.
	- If the current element is same as `element`, increase `count` by $1$, else decrease `count` by $1$.
- At the end, the element present in `element` should be the answer, if there exists an element which occurs more than $\frac{N}{2}$ times.
- However, if `element` is not in majority, then there exist no other answer. To confirm this, we can traverse the array once more.
```cpp
int majorityElement(vector<int> v)
{
	int n = v.size();
	int count = 0;
	int element;
	for (int i = 0; i < n; i++)
	{
		if (count == 0)
		{
			element = v[i];
			count++;
		}
		else if (element == v[i])
			count++;
		else
			count--;
	}
	// Optional: Check if stored element is majority
	int freq = 0;
	for (int i = 0; i < n; i++)
		if (element == v[i])
			freq++;
	if (freq > (n / 2))
		return element;
	else
		return -1;
}
```
- Time Complexity: $O(2\times N)$
- Space Complexity: $O(1)$
## Extended Voting Algorithm
> [!tip] Given an array of $N$ integers, write an algorithm to return the elements that occurs more than $\frac{N}{3}$ times in the given array.
- Keep a note of 4 variable `cnt1`, `cnt2`, `el1` and `el2`.
- Traverse through the array:
	- If `cnt1` is $0$ and current element is not `el2`, store the current element as `el1` and increase `cnt1` to $1$.
	- If `cnt2` is $0$ and current element is not `el1`, store the current element as `el2` and increase `cnt2` to $1$.
	- If current element is `el1` or `el2`, increase `cnt1` or `cnt2` by $1$ respectively.
	- Otherwise, decrease `cnt1` and `cnt2` by $1$.
- The values of `el1` and `el2` are the expected answers. Run another loop to manually check if their frequencies are greater than $\frac{N}{3}$.
```cpp
vector<int> majorityElement(vector<int> v)
{
	int n = v.size();
	int cnt1 = 0, cnt2 = 0;
	int el1, el2;
	for (int i = 0; i < n; i++)
	{
		if (cnt1 == 0 && el2 != v[i])
		{
			cnt1++;
			el1 = v[i];
		}
		else if (cnt2 == 0 && el1 != v[i])
		{
			cnt2++;
			el2 = v[i];
		}
		else if (el1 == v[i])
			cnt1++;
		else if (el2 == v[i])
			cnt2++;
		else
		{
			cnt1--;
			cnt2--;
		}
	}
	vector<int> ans;
	cnt1 = 0;
	cnt2 = 0;
	for (int i = 0; i < n; i++)
	{
		if (v[i] == el1)
			cnt1++;
		else if (v[i] == el2)
			cnt2++;
	}
	if (cnt1 > n / 3)
		ans.push_back(el1);
	if (cnt2 > n / 3)
		ans.push_back(el2);
	return ans;
}
```
- Time Complexity: $O(2\times N)$
- Space Complexity: $O(1)$