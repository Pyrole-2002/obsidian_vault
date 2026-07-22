- We are given an undirected graph. A bridge is defined as an edge which, when removed, makes the graph disconnected (increases the number of [[Connected Components]]). We will find all bridges in a given graph.
- The algorithm is based on [[DFS]] and has time complexity $O(V+E)$.
- Pick an arbitrary vertex of graph `root` and run DFS from it.
- At any point in DFS, the edge from $(v, to)$ is a bridge iff none of the vertices $to$ and its descendants in the DFS has a back-edge to vertex $v$ or any of its ancestors. This means that there is no other way from $v$ to $to$ except for edge $(v, to)$.
- Let `tin[v]` denote entry time for node $v$. The array `low[]` will let us check the condition for each vertex. `low[v]` is the minimum of `tin[v]`, the entry times `tin[p]` for each node $p$ that is connected to node $v$ via a back-edge $(v, p)$ and the values of `low[to]` for each vertex $to$ which is a direct descendant of $v$ in the DFS tree:
$$
\text{low}[v] = \min
\begin{cases}
\text{tin}[v]\\
\text{tin}[p], & \text{for all $p$ for which $(v, p)$ is a back edge}\\
\text{low}[to], & \text{for all $to$ for which $(v, to)$ is a tree edge}
\end{cases}
$$
- Now, there is a back edge from vertex $v$ or one of its descendants to one of its ancestors iff vertex $v$ has a child $to$ for which `low[to] <= tin[v]`. If `low[to] = tin[v]`, the back edge comes directly to $v$, otherwise it comes to one of the ancestors of $v$.
- Thus, the current edge $(v, to)$ in the DFS tree is a bridge iff `low[to] > tin[v]`.
##### Implementation
The implementation distinguishes three cases: when we go down the edge in DFS tree, when we find a back edge to an ancestor of the vertex and when we return to a parent of the vertex.
- `visited[to] = false` : the edge is part of DFS tree
- `visited[to] = true && to != parent` : the edge is back edge to one of its ancestors
- `to = parent` : the edge leads back to parent in DFS tree
For the cases of multiple edges, we need to be careful when ignoring the edge from the parent. We will add a flag `parent_skipped` which will ensure we only skip the parent once.
```cpp
vector<bool> visited;
vector<int> tin, low;
int timer;

void dfs(vector<vector<int>> adj, vector<pair<int, int>> &bridges, int v, int p = -1)
{
	visited[v] = true;
	tin[v] = low[v] = timer++;
	for (int to : adj[v])
	{
		if (to == p)
			continue;
		if (visited[to])
			low[v] = min(low[v], tin[to]);
		else
		{
			dfs(adj, bridges, to, v);
			low[v] = min(low[v], low[to]);
			if (low[to] > tin[v])
				bridges.emplace_back(v, to);
		}
	}
}

vector<pair<int, int>> findBridges(vector<vector<int>> adj, int n)
{
	timer = 0;
	visited.assign(n, false);
	tin.assign(n, -1);
	low.assign(n, -1);
	vector<pair<int, int>> bridges;
	for (int i = 0; i < n; i++)
		if (!visited[i])
			dfs(adj, bridges, i);
	return bridges;
}
```
## Finding Bridges Online
https://cp-algorithms.com/graph/bridge-searching-online.html

# Articulation Points
- We are given an undirected graph. An articulation point (or cut vertex) is defined as a vertex which, when removed along with its edges, makes the graph disconnected (increases the number of [[Connected Components]]). We will find all articulation points in a given graph.
- The algorithm is mostly same as that for finding bridges
- Pick an arbitrary vertex of the graph `root` and run DFS from it.
- At any point in DFS, when we are at vertex `v != root`, if the current edge $(v, to)$ is such that none of its vertices $to$ or it's descendants in the DFS traversal has a back edge to any of ancestors of $v$, then $v$ is an articulation point. Otherwise, $v$ is not an articulation point.
- The case where `v == root` is an articulation point iff this vertex has more than one child in the DFS tree.
##### Implementation
```cpp
vector<bool> visited;
vector<int> tin, low;
int timer;

void dfs(vector<vector<int>> adj, vector<int> &cut_points, int v, int p = -1)
{
	visited[v] = true;
	tin[v] = low[v] = timer++;
	int children = 0;
	for (int to : adj[v])
	{
		if (to == p)
			continue;
		if (visited[to])
			low[v] = min(low[v], tin[to]);
		else
		{
			dfs(adj, cut_points, to, v);
			low[v] = min(low[v], low[to]);
			if (low[to] >= tin[v] && p != -1)
				cut_points.push_back(v);
			children++;
		}
	}
	if (p == -1 && children > 1)
		cut_points.push_back(v);
}

vector<int> findCutPoints(vector<vector<int>> adj, int n)
{
	timer = 0;
	visited.assign(n, false);
	tin.assign(n, -1);
	low.assign(n, -1);
	vector<int> cut_points;
	for (int i = 0; i < n; i++)
		if (!visited[i])
			dfs(adj, cut_points, i);
	return cut_points;
}
```