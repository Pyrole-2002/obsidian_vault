- Unordered Map is a container that stores key-value and mapped-value pairs, like [[Map]].
- Internally, unordered map is implemented using [[Hash Table]], the key provided is hashed into indices of a hash table which is why performance depends on the hash function.
- On average, the cost of search, insert and delete is $O(1)$.
## Syntax
```cpp
unordered_map<object_type1, object_type2> variable_name;
```

### Functions
```cpp
unordered_map<string, int> m;

// insert(), emplace() and [] can be used to insert elements
m.insert("a", 1);
m.emplace("b", 2);
m["c"] = 3;

// begin() returns an iterator to the first element in the container
m.begin();

// end() returns an iterator to the theoretical element after the last element in the container
m.end();

// bucket() returns the bucket number wehre the key is located
m.bucket("a");

// bucket_count() returns the number of buckets in the container
m.bucket_count();

// bucket_size() returns the number of elements in a bucket (given bucket_number)
m.bucket_size(i);

// find() returns an iterator to the key
m.find("a");

// empty() returns true if the container is empty else false
m.empty();

// erase() erases a key in the container
m.erase("a");
```

#### Unordered Map vs [[Unordered Set]]
|                           Unordered Map                           |                           Unordered Set                            |
| :---------------------------------------------------------------: | :----------------------------------------------------------------: |
|         Contains elements in the form of key-value pairs          | Contains independent values, used to see presence/absence of a set |
| Operator `[]` can be used to extract corresponding value to a key |    Searching for an element is done using the `find()` function    |

#### Unordered Map vs [[Map]]
|                            Unordered Map                            |                             Map                              |
| :-----------------------------------------------------------------: | :----------------------------------------------------------: |
|                   Key can be stored in any order                    |               Ordered sequence of unique keys                |
| Implements an unbalanced tree structure, so order is not maintained | Implements a balanced tree structure, so order is maintained |
|          Time complexity of operations is generally $O(1)$          |    Time complexity of operations is generally $O(\log N)$    |
