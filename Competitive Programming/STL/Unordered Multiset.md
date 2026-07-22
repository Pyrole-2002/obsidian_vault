- The unordered multiset is an unordered container that works similarly to an [[Unordered Set]]. The only difference is that we can store multiple copies of the same key in this container.
- It is also implemented using a [[Hash Table]] so time complexity of operations is $O(1)$ on average and $O(N)$ in the worst case.
## Syntax
```cpp
unordered_multiset<object_type> variable_name;
```

### Functions
```cpp
unordered_multiset<int> s;

// insert() and emplace() inserts new elements
s.insert(1);
s.emplace(2);
s.emplace(2);

// begin() returns an iterator pointing to the first element in the container or the first element in one of its buckets
s.begin();

// end() returns an iterator pointing to the position immediately after the last element in the container
s.end();

// empty() returns true if container is empty otherwise false
s.empty();

// find() returns an iterator that points to the element position
s.find(1);

// clear() clears the contents of the container
s.clear();

// count() returns count of elements equal to given value
s.count(1);

// size() returns the count of elements in container
s.size();

// erase() removes a single element, or all elements with a value, or a range of elements
s.erase(2); // erases all elements with value 2
s.erase(itr);
s.erase(itr_start, itr_end);

// swap() swaps two containers
s1.swap(s2);

// equal_range() returns a pair, where first is iterator to first position of element and second is iterator to last position of element
s.equal_range(2);
```

#### Examples
```cpp
#include<bits/stdc++.h>

using namespace std;
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(NULL);
	
	unordered_multiset < int > s;
	s = {1, 2, 1, 1, 2, 3, 4, 5, 3, 3, 2, 1, 2, 1, 3, 2, 3, 4, 2, 1, 1, 1};
	
	cout << "Elements present in the unordered_multiset: ";
	for (auto i : s)
		cout << i << " ";
	cout << endl;
	
	cout << "Number of elements present in the unordered_multiset: " << s.size() << endl;
	
	cout << "Deleting all elements with value 2: ";
	s.erase(2);
	for (auto i : s)
		cout << i << " ";
	cout << endl;
	
	cout << "Deleting one copy of element with value 1: ";
	s.erase(s.find(1));
	for (auto i : s)
		cout << i << " ";
	cout << endl;
}
```
