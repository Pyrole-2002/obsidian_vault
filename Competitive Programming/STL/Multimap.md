- Multimap is similar to [[Map]] with the addition that multiple elements can have the same keys.
- It is not required that the key-value and mapped-value pairs be unique.
- Multimap keeps all the keys in sorted order always.
## Syntax
```cpp
multimap<key_type, value_type> variable_name;
```

### Functions
```cpp
multimap<int, int> m;

// clear() removes all elements from container
m.clear();

// empty() returns true if container is empty else false
m.empty();

// swaps() swaps the values in two containers
m1.swap(m2);

// size() returns the number of elements in container
m.size();

// insert() and emplace() add key-value pair to the container
m.insert(1, 2);
m.emplace(1, 4);

// begin() returns an iterator to the first element in the container
m.begin();

// end() returns an iterator to the theoretical element that follows the last element in the container
m.end();

// count() returns the number of matches with the key
m.count(1);

// erase() removes a key from the multimap (and all values associated with that key)
m.erase(1);
m.erase(itr); // erases just the key-value pair with that iterator
m.erase(itr_start, itr_end); // erases all key-value pairs in range

// find() returns an iterator to the key provided (first occurred)
m.find(1);
```