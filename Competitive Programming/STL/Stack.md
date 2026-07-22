- Stacks are containers with Last In First Out (LIFO), where a new element is added at top and an element is removed from top itself.
- Stack uses either [[Vector]], [[Deque]] (default) or [[List]] as its underlying container.
## Syntax
```cpp
stack<object_type> variable_name;
```

### Functions
```cpp
stack<int> s;

// empty() returns true if stack is empty else false
s.empty();

// size() returns the size of the stack
s.size();

// top() returns a reference to the top most element
s.top();

// push() and emplace() inserts an element to the top of stack
s.push(2);
s.emplace(3);

// pop() deletes the topmost element from stack
s.pop();
```