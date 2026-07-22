- Queues are container adaptors that operate in a First In First Out (FIFO) type of arrangement.
- Elements are inserted at the back (end) and are deleted from the front (start).
- Queues use an encapsulated object of [[Deque]] or [[List]] as its underlying container.
## Syntax
```cpp
queue<object_type> variable_name;
```

### Functions
```cpp
queue<int> q;

// empty() returns true if container is empty else false
q.empty();

// size() returns the size of container
q.size();

// swap() swaps the contents of two containers of same data type
q1.swap(q2);

// push() and emplace() adds a new element to the end of container
q.push(2);
q.emplace(2);

// front() returns a reference to the first element of container
q.front();

// back() returns a reference to the last element of container
q.back();

// pop() deletes the first element of container
q.pop();
```