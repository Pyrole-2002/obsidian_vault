- Lists are sequence containers that allow non-contiguous memory allocation.
- As compared to [[Vector]], the list has slow traversal, but once a position has been found, insertion and deletion happen in $O(1)$.
- Lists have faster insert and delete operations as compared to both arrays and vectors.
- Normally we use a [Doubly Linked List](https://www.geeksforgeeks.org/introduction-and-insertion-in-a-doubly-linked-list/). To implement a Singly Linked List, we use a [[Forward List]].
- Has only sequential access, random access to any middle element is not possible.
## Syntax
```cpp
list<object_type> variable_name;
```

### Functions
```cpp
list<int> l;

// front() returns the value of the first element in the list
l.front();

// back() returns the value of the last element in the list
l.back();

// push_front() and emplace_front() adds a new element at the beginning of the list
l.push_front(1);
l.emplace_front(1);

// push_back() and emplace_back() adds a new element at the end of the list
l.push_back(2);
l.emplace_back(2);

// pop_front() removes the first element of the list
l.pop_front();

// pop_back() removes the last element of the list
l.pop_back();

// insert() inserts given number of elements before the position mentioned
l.insert(itr, 10, 0); // inserts 10 elements with value 0 before itr

// emplace() inserts an element at a given position
l.emplace(itr, 2);

// begin() returns an iterator pointing to the first element of the list
l.begin();

// end() returns an iterator pointing to the element theoretically after the last element
l.end();

// empty() returns 1 if container is empty else 0
l.empty();

// erase() removes a single element or a range of elements
l.erase(itr);

// assign() assigns new elements by replacing current elements and resizing the container
l.assign(10, 0); // replaces first 10 elements with 0
l1.assign(l2.begin(), l2.end()); // copies elements from l2 into l1

// remove() removes all the elements from the list which are equal to given value
l.remove(2);

// reverse() reverses the list
l.reverse();
```