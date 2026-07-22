- Array classes knows its size, whereas C-style arrays lack this property. So when passing to functions, we don't need to pass size as a separate parameter.
- C-style arrays have a risk of [array being decayed into a pointer](https://www.geeksforgeeks.org/what-is-array-decay-in-c-how-can-it-be-prevented/). Array classes don't decay into pointers.
## Syntax
```cpp
array<object_type, size> variable_name;
```

### Functions
```cpp
array<int, 6> arr = {1, 2, 3, 4, 5, 6};

// at() is used to access the element at an index
arr.at(3);
// we can also use [] operator like C-style arrays
arr[3];

// front() returns reference to the first element of array
arr.front();

// back() returns reference to the last element of array
arr.back();

// size() returns the number of elements in array
arr.size();

// swap() swaps all elements of one array with other
a1.swap(a2);

// empty() returns true if array size is 0 else returns false
arr.empty();

// fill() fills the entire array with a value
arr.fill(0);
```