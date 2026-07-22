Iterators are used to point at the memory addresses of STL containers. They are primarily used in sequences. They reduce the complexity and execution time of the program.
## Syntax
```cpp
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int>::iterator ptr; // iterator to a vector<int>

ptr = v.begin(); // returns the beginning position of vector
ptr = v.end(); // returns the after end position of the container

advance(ptr, 3); // advances pointer, now pointing to v[0+3]

auto next_itr = next(ptr, 1); // new pointer returned advanced by 1 position
auto prev_itr = prev(ptr, 1); // new pointer returned decremented by 1 position

ptr += 1; // avoid using this as this does not work if memory is non-contiguous
ptr++; // works also for non-contiguous memory alocation

cout << *ptr << '\n'; // *ptr is used to access the value by reference
```