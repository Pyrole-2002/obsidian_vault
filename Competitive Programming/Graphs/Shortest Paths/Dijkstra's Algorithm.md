- We have a weighted [[Graphs Representation|Graph]] with $n$ vertices and $m$ edges. The weights of all edges are non-negative. We are given a starting vertex $s$. We will then find the lengths of the shortest paths from a starting vertex $s$ to all other vertices. This problem is also called single-source shortest paths problem.
- Create an array $d$ where for each vertex $v$ we store the current length of the shortest path from $s$ to $v$ in `d[v]`.
- Initially, `d[s] = 0`, and for all other vertices this length equals infinity.
$$
d[v] = \infty\quad \forall\quad v\ne s
$$
- We also maintain a boolean array $u$ which stores for each vertex $v$ whether it's marked. Initially, all vertices are unmarked.
- Dijkstra's Algorithm runs for $n$ iterations. At each iteration, a vertex $v$ is chosen as unmarked vertex which has least value of $d$.
- The selected vertex $v$ is marked, from $v$ relaxations are performed where all edges of the form $(v, to)$ are considered and for each vertex $to$, the algorithm tries to improve the value $d[to]$. If the length of the current edge is $len$, then relaxation is:
$$
d[to] = \min(d[to,\ d[v]+len])
$$
- After all the edges are considered, the current iteration ends. After $n$ iterations, all vertices will be marked, and the algorithm terminates.
- The found values of $d[v]$ are the lengths of shortest paths from $s$ to all vertices $v$.
### Restoring Shortest Paths
- If we want to know not only the lengths of the shortest paths but the paths themselves, we will maintain an array of predecessors $p$ in which for each vertex $v \ne s$, $p[v]$ is the penultimate vertex in the shortest path from $s$ to $v$.
- We use the fact that if we take the shortest path to some vertex $v$ and remove $v$ from this path, we'll get a path ending in at vertex $p[v]$, and this path will be the shortest for the vertex $p[v]$.
- For each successful relaxation, when for some selected vertex $v$, there is an improvement in the distance to some vertex $to$, we update the predecessor vertex for $to$ with the vertex $v$.
$$
p[to] = v
$$
#### Implementation
- For the simplest implementation, on each iteration vertex search requires $O(n)$ time and each relaxation can be performed in $O(1)$. Asymptotic time complexity becomes $O(V^2 + E)$.
- This complexity is optimal for dense graphs where $E\approx V^2$. However, in sparse graphs where $E$ is much smaller than maximum number of edges $V^2$, the problem can be solved in $O(n\log n+m)$. This will be discussed in future section.
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<int> d, p;
vector<bool> visited;

void dijkstra(int s)
{
	d[s] = 0;
	for (int i = 0; i < n; i++)
	{
		int v = -1; // vertex with min distance
		for (int j = 0; j < n; j++)
			if (!visited[j] && (v == -1 || d[j] < d[v]))
				v = j;
		if (d[v] == INF)
			break; // unreachable vertices
		visited[v] = true;
		for (auto edge : adj[v])
		{
			int to = edge.first;
			int len = edge.second;
			if (d[v] + len < d[to])
			{
				d[to] = d[v] + len;
				p[to] = v;
			}
		}
	}
}

vector<int> restorePath(int s, int t)
{
	vector<int> path;
	for (int v = t; v != s; v = p[v])
		path.push_back(v);
	path.push_back(s);
	reverse(path.begin(), path.end());
	return path;
}
```
## Dijkstra on Sparse Graphs
- In the simplest implementation, there are two operations, finding the unmarked vertex with smallest $d$ and the time of relaxation. The first operation is performed $O(V)$ times and requires $O(V)$ per operation, while the second operation is performed $O(E)$ times and requires $O(1)$ per operation making the overall complexity $O(V^2 + E)$.
- To improve the complexity for sparse graphs when $E$ is much smaller than $V^2$, we need to improve the complexity of the first operation without affecting the second significantly.
- The most efficient data structure for this is the **Fibonacci Heap** which allows the first operation to run in $O(\log V)$ and the second operation in $O(1)$. The overall complexity becomes $O(V\log V + E)$. However, they are very complex to implement and also have a large hidden constant.
- We will use data structures that perform both types of operations to give us a total time complexity of $O(n\log n + m\log n) \approx O(m\log n)$
### [[Set]] Implementation
- Since we need to store vertices ordered by their values $d$, it is convenient to store actual pairs with the distance and the index of the vertex. As a result in a set, pairs are automatically sorted by their distances.
- We no longer need the `visited[]` array because we use the set to store that information.
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<int> d, p;

void dijkstra(int s)
{
	d[s] = 0;
	set<pair<int, int>> q;
	q.insert({0, s});
	while (!q.empty())
	{
		int v = q.begin()->second;
		q.erase(q.begin());
		for (auto edge : adj[v])
		{
			int to = edge.first;
			int len = edge.second;
			if (d[v] + len < d[to])
			{
				q.erase({d[to], to});
				d[to] = d[v] + len;
				p[to] = v;
				q.insert({d[to], to});
			}
		}
	}
}
```
### [[Priority Queue]] Implementation
- The main difference between set and priority queue implementation is that we cannot remove elements from priority queue (heaps can support that operation).
- Therefore, we don't delete the old pair from the queue. As a result a vertex can appear multiple times with different distance in the queue at the same time. Among these pairs we only consider the first element equal to the corresponding value in $d$, all other pairs are old.
- In the modification, at the beginning of each iteration, after extracting the next pair, we check if it is an important pair or if it is already an old and handled pair. Without this check, the complexity will increase up to $O(V\times E)$.
- By default priority queue sorts elements in descending order. Either we sort the elements in ascending order or store the negated distances in it.
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<int> d, p;

void dijkstra(int s)
{
	d[s] = 0;
	priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
	q.push({0, s});
	while (!q.empty())
	{
		int v = q.top().second;
		int d_v = q.top().first;
		q.pop();
		if (d_v != d[v]) // Skip if outdated
			continue;
		for (auto edge : adj[v])
		{
			int to = edge.first;
			int len = edge.second;
			if (d[v] + len < d[to])
			{
				d[to] = d[v] + len;
				p[to] = v;
				q.push({d[to], to});
			}
		}
	}
}
```