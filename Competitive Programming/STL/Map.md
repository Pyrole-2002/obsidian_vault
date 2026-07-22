- Maps are containers that store elements in a mapped fashion.
- Each element has a key value and a mapped value.
- No two mapped values can have the same key values.
## Syntax
```cpp
map<key_type, value_type> variable_name;
```

### Functions
```cpp
map<string, int> m;

// inserting elements in a map, can use insert() and emplace() interchangebly
m["One"] = 1;
m.insert(pair<string, int>("Two", 2));
m.emplace(make_pair("Three", 3));
m.emplace({"Four", 4});
m.emplace("Five", 5);

// count() returns true if key is present else false
m.count("Four");

// find() returns an iterator to the element with matching key
pair<string, int>* p = m.find("Three"); // p->first gives the key, p->second gives the value

// size() returns the size of the map
m.size();

// empty() returns true if map is empty else returns false
m.empty();

// begin() returns an iterator to the first element in the map
m.begin();

// end() returns an iterator to the theoretical element that follows the last element in the map
m.end();

// clear() removes all elements from map
m.clear();
```