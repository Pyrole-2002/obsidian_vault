- Multiset is a type of container similar to [[Set]], with the exception that multiple elements can have the same values.
- Values are arranged in ascending order by default.
```cpp
multiset<object_type> variable_name;

// decreasing order multiset
multiset<object_type, greater<object_type>> variable_name;
```
## Syntax
```cpp
multiset<int> s;

// begin() returns an iterator to the first element in set
s.begin();

// end() returns an iterator to the theoretical element that follows the last element in the multiset
s.end();

// size() returns the size of the set
s.size();

// empty() returns true if the set is empty else false
s.empty();

// insert() and emplace() insert new elements to the set
s.insert(2);
s.emplace(2);
s.emplace(itr, 2);

// erase() removes an element
s.erase(itr);
s.erase(2);

// clear() removes all elements from set
s.clear();

// find() returns an iterator to the element in set
s.find(2);

// count() returns the number of elements having same value as parameter
s.count(2);
```