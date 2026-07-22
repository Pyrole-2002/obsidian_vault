- Given a weighted undirected [[Graphs Representation|graph]] $G$ with $V$ vertices and $E$ edges, we will find a spanning tree of this graph which connects all vertices and has the least sum of weights of all edges. A spanning tree is a set of edges such that any vertex can reach any other by exactly one simple path. The spanning tree with the least weight is called a Minimum Spanning Tree.
![[Pasted image 20240703014427.png]]![[Pasted image 20240703014433.png]]
- Any spanning tree consists of $V-1$ edges.
- The MST is built gradually by adding edges one at a time. At first the tree consists only of a single arbitrary vertex.
- Then the minimum weight edge outgoing from this vertex is selected and added to the MST. This also adds the corresponding vertex to the tree.
- Now select and add the edge with the minimum weight that has one end in an already selected vertex and the other end in an unselected vertex.
- Every time we select and add the edge with minimal weight that connects one selected vertex with one unselected vertex. This is repeated until the MST contains all vertices and $V-1$ edges.
### Trivial Implementation
- If we search the edge by iterating over all possible edges, then it takes $O(E)$ time to find the edge with minimum weight. The total complexity becomes $O(VE)$, in the worst case this becomes $O(V^3)$.
- If we only look at one edge from each already selected vertex, we can sort the edges from each vertex in ascending order of their weights, and store a pointer to the first valid edge.
- Then after finding and selecting the minimal edge, we update the pointers. This gives a complexity of $O(V^2+E)$ and for sorting the edges an additional $O(E\log V)$ which gives the complexity $O(V^2\log V)$ in the worst case.
## Dense Graphs
- For every not yet selected vertex, we will store the minimum edge to an already selected vertex. Then during a step, we only look at these minimum weight edges which take $O(V)$.
- After adding an edge some minimum edge pointers have to be recalculated. The weights can only decrease, the minimal weight edge of every non-selected vertex may stay the same or it will be updated by an edge to the newly selected vertex. This also takes $O(V)$.
- Therefore the total complexity becomes $O(V^2)$.
- This implementation is very convenient for the Euclidean MST where we have $V$ nodes on a plane and the distance between each pair of points is the Euclidean distance between them and we want to find an MST for this complete graph. This task can be solved by Prim's Algorithm in $O(V^2)$ time and $O(V)$ space which is not possible with [[Kruskal's Algorithm]].
```cpp
int n;
vector<vector<pair<int, int>>> adj;

vector<vector<pair<int, int>>> prim()
{
	vector<bool> selected(n, false);
	vector<vector<pair<int, int>>> mst(n);
	vector<int> min_edge(n, INF), parent(n, -1); // min edge weight to reach a vertex and its parent

	min_edge[0] = 0;
	for (int i = 0; i < n; i++)
	{
		int u = -1;
		for (int j = 0; j < n; j++)
			if (!selected[j] && (u == -1 || min_edge[j] < min_edge[u])) // find the vertex with the minimum edge weight
				u = j;

		if (min_edge[u] == INF) // if the vertex is not reachable
			return {};

		selected[u] = true;
		if (parent[u] != -1)
		{
			mst[u].push_back({parent[u], min_edge[u]});
			mst[parent[u]].push_back({u, min_edge[u]});
		}

		for (auto e : adj[u])
		{
			int v = e.first, w = e.second;
			if (w < min_edge[v])
			{
				min_edge[v] = w;
				parent[v] = u;
			}
		}
	}

	return mst;
}
```
## Sparse Graphs
- It is possible to interpret the operations of finding the minimum and modifying some values as [[Set]] operations.
- We can now find the minimum edge in $O(\log V)$ time but recomputing the pointers will now take $O(V\log V)$. When we consider that we only need to update $O(E)$ times in total and perform $O(V)$ searches for the minimal edge, then the total complexity will be $O(E\log V)$.
- For sparse graphs this is better but for dense graphs this will be slower.
```cpp
vector<vector<pair<int, int>>> prim()
{
	int total_weight = 0; // total weight of the MST
	vector<bool> selected(n, false);
	vector<vector<pair<int, int>>> mst(n);
	vector<int> min_edge(n, INF), parent(n, -1); // min edge weight to reach a vertex and its parent

	min_edge[0] = 0;
	set<pair<int, int>> s; // {min_edge_weight, vertex}
	s.insert({0, 0});

	for (int i = 0; i < n; i++)
	{
		if (s.empty())
			return {};

		int u = s.begin()->second;
		s.erase(s.begin());
		selected[u] = true;
		total_weight += min_edge[u];

		if (parent[u] != -1)
		{
			mst[u].push_back({parent[u], min_edge[u]});
			mst[parent[u]].push_back({u, min_edge[u]});
		}

		for (auto edge : adj[u])
		{
			int v = edge.first, w = edge.second;
			if (!selected[v] && w < min_edge[v])
			{
				s.erase({min_edge[v], v});
				min_edge[v] = w;
				parent[v] = u;
				s.insert({min_edge[v], v});
			}
		}
	}

	return mst;
}
```