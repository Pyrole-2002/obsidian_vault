- Given a weighted directed graph $G$ with $V$ vertices and $E$ edges, and some vertex $v$, we will find the length of shortest paths from vertex $v$ to every other vertex.
- Unlike [[Dijkstra's Algorithm]], this can also be applied to graphs containing negative weight edges. However, if the graph contains a negative cycle, then the shortest path to some vertices may not exist. This algorithms can also detect the presence of a cycle of negative weight and deduce this cycle.
- First assume that the graph contains no negative weight cycle. We create a distance array $d$ of size $V-1$, which after completion of the algorithm will contain the answer to the problem. Initialize `d[v] = 0`, and all other elements of $d$ to $\infty$.
- The algorithm consists of phases, each phase scans through all edges of the graph, and the algorithm tries to produce relaxation along each edge $(a, b)$ having weight $c$. Relaxation attempts to improve the value of $d[b]$ using value of $d[a]+c$.
- $n-1$ phases of relaxation are sufficient to correctly calculate the lengths of all shortest paths in the graph. For unreachable vertices, $d$ remains $\infty$.
- We check if $a$ is less than $\infty$ to skip the edges where path to $a$ has not yet been found.
- Time Complexity: $O(VE)$
## Proof
- Note that for all unreachable vertices $u$, the algorithm works correctly as the $d[u]$ remains $\infty$.
- To Prove: After the execution of $i_{\text{th}}$ phase, the algorithm correctly finds all shortest paths whose number of edges doesn't exceed $i$.
- Consider an arbitrary vertex $a$ to which there is a path from the starting vertex $v$, and consider a shortest path to it $(p_{0}=v, \dots, p_{k}=a)$.
- Before the first phase, the shortest path to $p_{0}$ was found correctly. During first phase, the edge $(p_{0}, p_{1})$ has been checked and the distance to the vertex $p_{1}$ was found correctly. Repeating this $k$ times will find the distance to the vertex $p_{k}$ correctly.
- Any shortest path can't have more than $n-1$ edges..
##### Simplest Implementation
```cpp
struct Edge
{
	int a, b, cost;
};

int n;
vector<Edge> edges;
vector<int> d;

void bellmanFord(int v)
{
	d.resize(n, INF);
	d[v] = 0;
	for (int i = 0; i < n - 1; i++)
		for (auto e : edges)
			if (d[e.a] < INF)
				d[e.b] = min(d[e.b], d[e.a] + e.cost);
}
```
##### Better Implementation
- To speed up this algorithm, we keep a flag to tell whether relaxation happened in the current phase or not, if in any phase relaxation had no change, the algorithm can be stopped.
- We can modify the algorithm so it allows to reconstruct the shortest paths.
```cpp
struct Edge
{
	int a, b, cost;
};

int n;
vector<Edge> edges;
vector<int> d, p;

void bellmanFord(int v)
{
	d.resize(n, INF);
	p.resize(n, -1);
	d[v] = 0;
	bool flag = false;
	for (int i = 0; i < n - 1; i++)
	{
		for (auto e : edges)
			if (d[e.a] < INF && d[e.a] + e.cost < d[e.b])
			{
				d[e.b] = d[e.a] + e.cost;
				p[e.b] = e.a;
				flag = true;
			}
		if (!flag)
			break;
	}
}
```
### Negative Cycles
- If there is a negative cycle reachable from $v$, the distances to the vertices in this cycle should be $-\infty$.
- To check for these, we run the algorithm for one more phase after phase $n-1$ and if this relaxation changes any value in $d$, this means the graph contains a negative cycle reachable from $v$.
- To retrieve this cycle as a sequence of vertices contained in it, we remember the last vertex $x$ for which there was a relaxation in the $n_{\text{th}}$ phase. This will either lie in a negative weight cycle or is reachable from it.
- To get the vertices that are guaranteed to lie in a negative cycle, starting from vertex $x$, pass through to the predecessors $n$ times. We will get the vertex $y$ which in the cycle is the earliest reachable from source.
```cpp
struct Edge
{
	int a, b, cost;
};

int n;
vector<Edge> edges;
vector<int> d, p, cycle;

void bellmanFord(int v)
{
	d.resize(n, INF);
	p.resize(n, -1);
	int x;
	d[v] = 0;
	bool flag = false;
	for (int i = 0; i < n; i++)
	{
		x = -1; // to check if there is a negative cycle
		for (auto e : edges)
			if (d[e.a] < INF && d[e.a] + e.cost < d[e.b])
			{
				d[e.b] = d[e.a] + e.cost;
				p[e.b] = e.a;
				flag = true;
				x = e.b;
			}
		if (!flag)
			break;
	}
	if (x != -1) // negative cycle
	{
		int y = x;
		for (int i = 0; i < n; i++)
			y = p[y];
		for (int cur = y;; cur = p[cur])
		{
			cycle.emplace_back(cur);
			if (cur == y && cycle.size() > 1)
				break;
		}
		reverse(cycle.begin(), cycle.end());
	}
}
```
# Shortest Path Faster Algorithm (SPFA)
- SPFA is an improvement of the Bellman-Ford Algorithm which takes advantage of the fact that not all attempts at relaxation will work.
- We create a queue containing only the vertices that were relaxed but still could further relax their neighbors. Whenever we relax some neighbor, we put it in the queue.
- The worst case time complexity of this algorithm is $O(VE)$ which is the same as that of Bellman-Ford but in practice its average complexity is around $O(E)$.
- There is no reason to put a vertex in the queue if it is already in.
```cpp
struct Edge
{
	int a, b, cost;
};

int n;
vector<Edge> edges;
vector<int> d, p, cycle;

void SPFA(int v)
{
	d.resize(n, INF);
	p.resize(n, -1);
	vector<int> cnt(n, 0); // number of times a vertex is in the queue
	vector<bool> in_queue(n, false); // whether a vertex is in the queue or not
	queue<int> q;

	d[v] = 0;
	q.push(v);
	in_queue[v] = true;
	while (!q.empty())
	{
		int u = q.front();
		q.pop();
		in_queue[u] = false;
		for (auto e : edges)
		{
			if (e.a == u) // only outgoing edges from u
			{
				if (d[e.b] > d[u] + e.cost)
				{
					d[e.b] = d[u] + e.cost;
					p[e.b] = u;
					if (!in_queue[e.b])
					{
						q.push(e.b);
						in_queue[e.b] = true;
						cnt[e.b]++;
						if (cnt[e.b] > n) // negative cycle detected
						{
							cycle.resize(n);
							int x = e.b;
							for (int j = 0; j < n; j++)
								x = p[x];
							for (int j = 0; j < n; j++)
							{
								x = p[x];
								cycle[j] = x;
								if (x == e.b)
									break;
							}
							return;
						}
					}
				}
			}
		}
	}
}
```