- It is well-known that we can find the shortest paths between a single source and all other vertices in $O(E)$ using [[BFS]] in an unweighted graph.
- An unweighted graph may be interpreted as a weighted graph with all edge weights equal to $1$. If all edge weights are different, then a more general algorithm like [[Dijkstra's Algorithm]] is appropriate.
- However, if the weight of each edge is either $0$ or $1$, then we can use BFS to solve the single source shortest path problem in $O(E)$.
- In Dijkstra's Algorithm, using priority queue implementation, we can see that the distances between the source $s$ and two other vertices in the queue differ by at most one.
- We know that $d[v]\le d[u]\le d[v]+1$ for each $u\in Q$. This is because we only add vertices with equal distances or with distance plus one to the queue during each iteration.
- Assume there exists a $u$ in the queue with $d[u]-d[v] > 1$, then $u$ must have been inserted in the queue via a different vertex $t$ with $d[t]\ge d[u]-1>d[v]$. However, this is impossible since the algorithm iterates over the vertices in increasing order.
- We don't need a priority queue because of simple structure. We use a normal queue and append new vertices at the beginning if the corresponding edge has weight $0(d[u] = d[v])$ or at the end if the edge has weight $1 (d[u] = d[v]+1)$. This way, the queue remains sorted all the time.
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<int> d;

void BFS01(int s)
{
	d[s] = 0;
	deque<int> q;
	q.emplace_front(s);
	while (!q.empty())
	{
		int u = q.front();
		q.pop_front();
		for (auto edge : adj[u])
		{
			int v = edge.first;
			int w = edge.second;
			if (d[u] + w < d[v])
			{
				d[v] = d[u] + w;
				if (w == 0)
					q.emplace_front(v);
				else
					q.emplace_back(v);
			}
		}
	}
}
```
## Dial's Algorithm
We can extend this further if we allow the weights of the edges to be even bigger. If every edge in the graph has $wt\le k$, then the distances of vertices in the queue will differ by at most $k$ from the distance of $v$ to the source. So we can keep $k+1$ buckets for the vertices in the queue, and whenever the bucket corresponding to the smallest distance gets empty, we make a cyclic shift to get the bucket with the next higher distance.