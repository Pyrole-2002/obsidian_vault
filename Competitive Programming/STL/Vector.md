- Vectors are dynamic arrays that have the ability to change size whenever elements are added or deleted from them. Vector elements can be easily accessed and traversed using iterators.
- A vector stores elements in contiguous memory locations.
- Inserting at the end takes differential time, as the array may need to be extended.
- Removing the last element takes constant time as no resizing happens.
- Inserting and removing at the beginning or in middle is linear in time.
## Syntax
```cpp
vector<object_type> variable_name;

// initialization using list
vector<object_type> variable_name({value1, value2, value3});

// initializing every element of the vector the same value with given size
vector<object_type> variable_name(size, value);

// initialization from another vector
vector<object_type> new_vector(old_vector);
```

### Functions
```cpp
vector<int> v1;
vector<string> v2;

// begin() returns an iterator pointing to the first element of the vector
auto itr = v1.begin();

// end() returns an iterator pointing to the element theoretically after the last element of the vector
auto itr = v1.end();

// push_back() and emplace_back() insert the element at the end of the vector
v1.push_back(1);
v1.emplace_back(2);

// insert() and emplace() are used to insert an element at a specific location
v1.insert(itr, 5);
v1.emplace(itr, 5);

// erase() is used to delete a specific element
v1.erase(itr);

// pop_back() deletes the last element of the vector and returns it
v1.pop_back();

// front() returns a reference to the first element of the vector
v1.front();

// back() returns a reference to the last element of the vector
v1.back();

// clear() deletes all the elements from the vector
v1.clear();

// empty() checks if the vector is empty or not
v1.empty();

// size() returns the size of the vector
v1.size();

// swap() swaps the contents of one vector with another vector of same type. Sizes may differ
v1.swap(v2);
```

#### Examples
```cpp
#include<bits/stdc++.h>

using namespace std;

int main()
{
	vector < int > v;

	for (int i = 0; i < 10; i++)
	{
		v.push_back(i); //inserting elements in the vector
	}

	cout << "the elements in the vector: ";
	for (auto it = v.begin(); it != v.end(); it++)
	    cout << * it << " ";

	cout << "\nThe front element of the vector: " << v.front();
	cout << "\nThe last element of the vector: " << v.back();
	cout << "\nThe size of the vector: " << v.size();
	cout << "\nDeleting element from the end: " << v[v.size() - 1];
	v.pop_back();

	cout << "\nPrinting the vector after removing the last element:" << endl;
	for (int i = 0; i < v.size(); i++)
		cout << v[i] << " ";

	cout << "\nInserting 5 at the beginning:" << endl;
	v.insert(v.begin(), 5);
	cout << "The first element is: " << v[0] << endl;
	cout << "Erasing the first element" << endl;
	v.erase(v.begin());
	cout << "Now the first element is: " << v[0] << endl;

	if (v.empty())
		cout << "\nvector is empty";
	else
		cout << "\nvector is not empty" << endl;

	v.clear();
	cout << "Size of the vector after clearing the vector: " << v.size();
}
```