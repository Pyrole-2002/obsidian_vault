- Pair is used to combine two values of any data types.
- It is used if we want to store tuples.
- The first element is referenced as `first` and the second element is referenced as `second`.
- Pairs can be assigned, copied and compared.
- The array of objects allocated in a [[Map]] or [[Hash Map]] is of type pair.
## Syntax
```cpp
pair<object_type1, object_type2> variable_name;
```

### Functions
```cpp
// different ways of initializing pairs
pair<int, int> p1(1, 2);
pair<int, int> p2 = make_pair(3, 4);
pair<int, int> p3(p2);
pair<int, int> p4 = p3;
pair<int, int> p5 = {5, 6};
pair<int, int> p6;
p6.first = 7;
p6.second = 8;
pair<int, int>* p7 = new pair<int, int>(9, 10); // pointer

// accessing values of pair class
p1.first;
p1.second;

// accessing values of pair pointer
p7->first;
p7->second;

// swap() swaps contents of 2 pairs with same types
p1.swap(p2);
```