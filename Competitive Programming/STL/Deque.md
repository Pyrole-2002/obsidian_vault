- Double-Ended Queues are containers with the feature of expansion and contraction on both ends.
- Similar to [[Vector]] but are more efficient in case of insertion and deletion of elements.
- Unlike vectors, contiguous storage allocation may not be guaranteed.
- Deque functions are the same as vector functions with an addition of push and pop operations for both front and back.
- Accessing elements is $O(1)$, insertion and removal of elements in middle is $O(N)$, insertion or removal of elements at start and end is $O(1)$.
## Syntax
```cpp
deque<object_type> variable_name;
```