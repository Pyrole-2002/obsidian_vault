- Priority Queue is a container designed such that the first element of the queue is either the greatest or the smallest of all elements in the queue, and elements are in non-increasing or non-decreasing order.
- The top element is always the greatest by default (can also change it to smallest).
- Priority Queue is built on top of the max heap and use an [[Array]] or [[Vector]] as an internal structure. It is the implementation of [[Heap]] data structure.
## Syntax
```cpp
priority_queue<object_type> variable_name;

// min heap for priority queue
priority_queue<object_type, vector<object_type>, greater<object_type>> variable_name;
```

### Functions
```cpp
priority_queue<int> q;

// empty() returns true if container is empty else false
q.empty();

// size() returns the size of the container
q.size();

// top() returns a reference to the topmost element of queue
q.top();

// push() and emplace() inserts a new element at the end of queue
q.push(1);
q.emplace(2);

// swap() swaps the contents of two containers of same data type
q1.swap(q2);
```