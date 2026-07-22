- A set is a container that stores unique elements in a specific sorted order.
- Every operation on a set takes $O(1)$ complexity in the average case and takes $O(N)$ in the worst case.
- Sets follow [[Binary Search Tree]] implementation.
- The values in a set are unindexed.
- By default, the set is sorted in ascending order. However, we can change the sorting order.
- Time complexity for insertion and deletion of elements in a set is $O(\log N)$.
## Syntax
```cpp
set<object_type> variable_name;

// change sorting order to descending
set<object_type, greater<object_type>> variable_name;
```

### Functions
```cpp
set<int> s;
set<string> str;

// insert() and emplace() inserts an element in the set
s.insert(1);
s.emplace(2);

// begin() returns an iterator pointing to the first element in the set
s.begin();

// end() returns an iterator to the theoretical element after the last element
s.end();

// count() returns 1 if the element is present in the container otherwise 0
s.count(1);

// clear() deletes all the elements in the set
s.clear();

// find() searches an element in the set
s.find(1);

// erase() deletes a single element
s.erase(s.begin());
s.erase(2);

// size() returns the size of the set
s.size();

// empty() checks if the set is empty or not
s.empty();
```

#### Examples
```cpp
#include<bits/stdc++.h>

using namespace std;

int main()
{
	set < int > s;
	for (int i = 1; i <= 10; i++)
	{
		s.insert(i);
	}

	cout << "Elements present in the set: ";
	for (auto it = s.begin(); it != s.end(); it++)
	{
		cout << * it << " ";
	}
	cout << endl;
	int n = 2;
	if (s.find(2) != s.end())
		cout << n << " is present in set" << endl;

	s.erase(s.begin());
	cout << "Elements after deleting the first element: ";
	for (auto it = s.begin(); it != s.end(); it++)
	{
		cout << * it << " ";
	}
	cout << endl;
	
	cout << "The size of the set is: " << s.size() << endl;

	if (s.empty() == false)
		cout << "The set is not empty " << endl;
	else
		cout << "The set is empty" << endl;
	s.clear();
	cout << "Size of the set after clearing all the elements: " << s.size();
}
```
