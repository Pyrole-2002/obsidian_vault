- The limitation with [[Unordered Map]] is that we cannot store duplicates in it.
- The internal implementation is the same as unordered map but for duplicate keys, another count value is maintained with each key-value pair.
- Pairs with the same keys come together in the data structure but pairs with the same values aren't guaranteed to come together.
## Syntax
```cpp
unordered_multimap<object_type1, object_type2> variable_name;
```

### Functions
```cpp
unordered_multimap<string, int> m;

// begin() returns an iterator to the first element in the container or one of its buckets
m.begin();

// end() returns an iterator to the theoretical element after the last element
m.end();

// count() returns the number of elements in container whose key matches with parameter
m.count("a");

// clear() clears the contents of container
m.clear();

// size() returns the number of elements in the container
m.size();

// swap() swaps the contents of two containers
m1.swap(m2);

// find() returns an iterator to the element with matching key
m.find("a");

// bucket_size() returns the number of elements in a bucket
m.bucket_size(i);

// bucket() returns the bucket number with a matchin key
m.bucket("a");

// bucket_count() returns the total number of buckets in container
m.bucket_count();

// empty() ertuns true if container is empty else false
m.empty();

// emplace() inserts a key-value pair in the container
m.emplace("a", 1);
```