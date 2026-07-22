- Given a graph with $V$ vertices and $E$ edges with weights $w_{i}$ and a starting vertex $v_{0}$, we will find the shortest path from $v_{0}$ to every other vertex.
- This algorithm works faster than [[Dijkstra's Algorithm]] and [[Bellman-Ford Algorithm]] in most cases and will also work for negative edges. However, this does not work for negative cycles.
- Let the array $d$ contain the shortest path lengths where $d_{i}$ is the length of shortest path from $v_{0}$ to vertex $i$. Initially $d$ is filled with $\infty$ for every vertex.
- Let array $p$ contain the ancestors where $p_{i}$ is the direct ancestor of the vertex $i$ on the path from $v_{0}$ to $i$.
- At each step, three sets of vertices are maintained:
	- $M_{0}$ vertices are those for which the distance has already been calculated (might not be the final distance)
	- $M_{1}$ vertices are those for which the distance is currently being calculated
	- $M_{2}$ vertices are those for which the distance has not yet been calculated
- The vertices in $M_{1}$ are stored in a [[Deque]]
- At each step we take a vertex $u$ from $M_{1}$ (from front of deque). We put this vertex $u$ into $M_{0}$. Then we iterate over all outgoing edges from this vertex. If $v$ is the other end of the current edge with weight $w$:
	- If $v\in M_{2}$, then $v$ is inserted into $M_{1}$ at the back of the deque and $d_{v} = d_{u}+w$
	- If $v\in M_{1}$, then we try to improve the value of $d_{v} =\min(d_{v}, d_{u}+w)$. Since $v$ is already in $M_{1}$, we don't need to insert it again.
	- If $v\in M_{0}$, and if $d_{v}$ can be because $d_{v}>d_{u}+w$, then improve $d_{v}$ and insert the vertex $v$ back to $M_{1}$ at the beginning.
- We will use the array $m$ to store in which set each vertex is.
#### Implementation
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<int> d, p;

void dEsopoPape(int v0)
{
	d[v0] = 0;
	vector<int> m(n, 2);
	deque<int> q;
	q.push_back(v0);

	while (!q.empty())
	{
		int u = q.front();
		q.pop_front();
		m[u] = 0;
		for (auto e : adj[u])
			if (d[e.first] > d[u] + e.second)
			{
				d[e.first] = d[u] + e.second;
				p[e.first] = u;
				if (m[e.first] == 2)
				{
					m[e.first] = 1;
					q.push_back(e.first);
				}
				else if (m[e.first] == 0)
				{
					m[e.first] = 1;
					q.push_front(e.first);
				}
			}
	}
}
```
The algorithm performs very fast in most cases however there exist cases for which the algorithm takes exponential time making it unsuitable in the worst case.