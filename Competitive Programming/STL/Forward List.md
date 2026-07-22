- Implements singly [[Linked List]]. Forward lists are more useful in insertion, removal and moving operations (like sorting).
- It differs from the [List] by the fact that forward list keeps track of the location of only the next element while list keeps track of both next and previous elements, thus increasing storage space.
- The drawback is that it cannot be iterated backward and its individual elements cannot be accessed directly.
## Syntax
```cpp
forward_list<object_type> variable_name;
```

### Functions
```cpp
forward_list<int> l;

// assign() assigns new elements by replacing current elements and resizing the container
l.assign(10, 0); // replaces first 10 elements with 0
l.assign({1, 2, 3});
l1.assign(l2.begin(), l2.end()); // copies elements from l2 into l1

// push_front() and emplace_front() adds a new element at the beginning of the container
l.push_front(1);
l.emplace_front(2);

// pop_front() removes the first element of the container
l.pop_front();

// insert_after() and emplace_after() inserts elements after any position.
l.insert_after(itr, {1, 2, 3});
l.emplace_after(itr, 4);

// erase_after() erases elements from a particular position
l.erase_after(itr);
l.erase_after(itr_start, itr_end); // erases all values between itr_start and itr_end

// remove() removes all occurrences of element
l.remove(2);

// clear() deletes all elements in container
l.clear();
```