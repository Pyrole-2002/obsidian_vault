## Stack Modification
- We will modify the stack data structure in such a way, that it is possible to find the smallest element in the stack in $O(1)$ time, while maintaining the same asymptotic behavior for adding and removing elements from the stack.
- To do this, we will store the elements in the stack in pairs, the element itself and the minimum in the stack starting from this element and below.
- To find the minimum in the whole stack, we just look at the value `stack.top().second`.
```cpp
// Declaration
stack<pair<int, int>> s;

// Adding an element
int new_min = s.empty() ? new_elem : min(new_elem, s.top().second);
s.emplace({new_elem, new_min});

// Remove an element
int rem_elem = s.top().first;
s.pop();

// Find the minimum
int min = s.top().second;
```
## Queue Modification
### Method 1
- We want to add elements at the end and remove them from the front.
- The disadvantage is that the modified queue will actually not store all the elements.
- The idea is to only store the elements in the queue that are needed to determine the minimum.
- The queue is kept in non-decreasing order (head/front is smallest).
- While adding a new element, we remove all elements from the back of the queue that are larger than the new element.
- While removing elements, we only remove the front element if it matches the value we want to remove. If it doesn't match, we do nothing.
```cpp
// Declaration
deque<int> q;

// Find the minimum
int min = q.front();

// Add an element
while (!q.empty() && q.back() > new_elem)
	q.pop_back();
q.emplace_back(new_elem);

// Remove an element
if (!q.empty() && q.front() == rem_elem)
	q.pop_front();
```
### Method 2
- Modification of method 1.
- We want to remove elements without knowing which element we have to remove.
- We do this by storing the index for each element in the queue, and remember how many elements we already have added and removed.
```cpp
// Declaration
deque<pair<int, int>> q;
int cnt_added = 0, cnt_removed = 0;

// Find the minimum
int min = q.front().first;

// Add an element
while (!q.empty() && q.back().first > new_elem)
	q.popback();
q.emplace_back({new_elem, cnt_added});
cnt_added++;

// Remove an element
if (!q.empty() && q.front().second == cnt_removed)
	q.pop_front();
cnt_removed++;
```
### Method 3
- In this method, we actually store all elements and still find the minimum in $O(1)$. We can also remove an element from the front without knowing its value.
- We will simulate a queue using two stacks.
- Make two stacks $s_1$ and $s_2$. These stacks will be modified as discussed before so that we can find the minimum in $O(1)$.
- We now add new elements to the stack $s_1$, and remove elements from the stack $s_2$.
- If at any time the stack $s_2$ is empty, we move all elements from $s_1$ to $s_2$ (reversing the order of those element).
- Finally, finding the minimum in the queue involves just finding the minimum of both stacks.
```cpp
// Decaration
stack<pair<int, int>> s1, s2;

// Find the minimum
int min;
if (s1.empty() || s2.empty())
	min = s1.empty() ? s2.top().second : s1.top().second;
else
	min = min(s1.top().second, s2.top().second);

// Add an element
int min = s1.empty() ? new_elem : min(new_elem, s1.top().second);
s1.emplace({new_elem, min});

// Remove an element
if (s2.empty())
{
	while (!s1.empty())
	{
		int element = s1.top().first;
		s1.pop();
		int min = s2.empty() ? element : min(element, s2.top().second);
		s2.push({element, min});
	}
}
int rem_elem = s2.top().first;
s2.pop();
```
#### Finding the Minimum for All Subarrays of Fixed Length
Suppose we are given an array $A$ of length $N$ and a given $M\le N$. We have to find the minimum of each subarray of length $M$ in this array. We need to solve this problem in linear time $O(n)$.
In the solution, we use any modified queue, we add the first $M$ elements of the array, find and output its minimum, then keep iterating by adding the next elements from the array and outputting the minimums.