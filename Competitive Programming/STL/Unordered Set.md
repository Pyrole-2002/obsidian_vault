- An unordered set is a container that stores unique elements in no particular order.
- It is implemented using a [[Hash Table]] where keys are hashed into indices of a hash table so that the insertion is always randomized.
- Every operation on an unordered set takes $O(1)$ complexity in the average case and takes $O(N)$ in the worst case which depends on the internally used hash function.

## Syntax
```cpp
unordered_set<object_type> variable_name;
```

### Functions
```cpp
unordered_set<int> s;
unordered_set<string> str;

// insert() and emplace() inserts an element in the unordered set
s.insert(1);
s.insert(2);
s.emplace(3);

// begin() returns an iterator pointing to the first element in the unordered set
s.begin();

// end() returns an iterator to the theoretical element after the last element
s.end();

// count() returns 1 if the element is present in the container otherwise 0
s.count(2);

// clear() deletes all the elements in unordered set
s.clear();

// find() searches an element in the unordered set
s.find(2);

// erase() deletes a single element or elements in a particular range
s.erase(s.begin());

// size() returns the size of the unordered set
s.size();

// empty() checks if the unordered set is empty or not
s.empty();
```

#### Examples
```cpp
#include<bits/stdc++.h>

using namespace std;

int main()
{
	unordered_set < int > s;
	for (int i = 1; i <= 10; i++)
	{
		s.insert(i);
	}

	cout << "Elements present in the unordered set: ";
	for (auto it = s.begin(); it != s.end(); it++)
	{
		cout << * it << " ";
	}
	cout << endl;
	int n = 2;
	if (s.find(2) != s.end())
		cout << n << " is present in unordered set" << endl;

	s.erase(s.begin());
	cout << "Elements after deleting the first element: ";
	for (auto it = s.begin(); it != s.end(); it++)
	{
		cout << * it << " ";
	}
	cout << endl;
	
	cout << "The size of the unordered set is: " << s.size() << endl;

	if (s.empty() == false)
		cout << "The unordered set is not empty " << endl;
	else
	    cout << "The unordered set is empty" << endl;
	s.clear();
	cout << "Size of the unordered set after clearing all the elements: " << s.size();
}
```

|                                      Set                                       |                              Unordered Set                               |
| :----------------------------------------------------------------------------: | :----------------------------------------------------------------------: |
|                       Stores elements in a sorted order                        |                   Stores elements in an unsorted order                   |
|                 Uses [[Binary Search Tree]] for implementation                 |                  Uses [[Hash Table]] for implementation                  |
| More than one element can be erased by giving the starting and ending iterator | Only that element can be erased for which the iterator position is given |
