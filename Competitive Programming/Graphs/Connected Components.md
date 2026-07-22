> [!tip] Given an undirected graph $G$ with $V$ nodes and $E$ edges, find in it all the connected components, i.e. several groups of vertices such that within a group each vertex can be reached from another and no path exists between different groups.
- We can use [[DFS]] or [[BFS]]. We will do a series of rounds of DFS.
- The first round will start from first node and all nodes in the first connected component will be traversed. Then we traverse the first unvisited node of the remaining nodes and run DFS on it thus finding the second connected component. Repeat the process until all nodes are visited.
- Time Complexity: $O(V+E)$
```cpp
vector<bool> visited;
vector<int> component;

void DFS(vector<vector<int>> adj, int vertex)
{
	visited[vertex] = true;
	component.emplace_back(vertex);
	for (int i : adj[vertex])
		if (!visited[i])
			DFS(adj, i);
}

vector<vector<int>> connectedComponents(vector<vector<int>> adj)
{
	vector<vector<int>> components;
	for (int i = 0; i < adj.size(); i++)
	{
		if (!visited[i])
		{
			component.clear();
			DFS(adj, i);
			components.emplace_back(component);
		}
	}
	return components;
}
```
## Condensation Graph
Given a directed graph $G$ with vertices $V$ and edges $E$:
**Strongly Connected Component:** is a maximal subset of vertices $C$ such that any two vertices of this subset are reachable from each other. For any $u, v\in C$:
$$
u\mapsto v,  v\mapsto u
$$
where $\mapsto$ means existence of the path from first vertex to the second.
- Strongly connected components do not intersect each other and it is a partition of all graph vertices.
- Thus we can give a definition of condensation graph $G^\text{SCC}$ as a graph containing every strongly connected component as one vertex.
- Each vertex of the condensation graph corresponds to the SCC of graph $G$. There is an oriented edge between two vertices $C_{i}$ and $C_{j}$ of the condensation graph iff there are two vertices $u\in C_{i},\ v\in C_{j}$ such that there is an edge in initial graph $(u, v)$.
- Condensation graph is acyclic. Suppose there is an edge between $C$ and $C'$, then there can't be an edge from $C'$ to $C$.
### Kosaraju Algorithm
- Based on [[DFS]] with time complexity $O(V+E)$ as it uses two DFS.
- We start at each vertex of the graph and run a DFS from every non-visited vertex. For each vertex we keep track of exit time `tout[v]`.
- The exit time `tout[C]` from the SCC $C$ is the maximum of values `tout[v]` for all $v\in C$. Similarly, the entry time `tin[C]` from SCC $C$ is the minimum of values `tin[v]` for all $v\in C$.
- Let $C$ and $C'$ be two different SCC and there be an edge $(C, C')$ in the condensation graph between these two nodes. Then `tout[C] > tout[C']`. Depending on difference between `tin[C]` and `tin[C']`:
	- If the component $C$ was reached first, it means that DFS comes at some vertex $v\in C$ at some moment, but all other vertices of components $C$ and $C'$ were not visited yet. By condition there is an edge $(C, C')$ in a condensation graph, so not only the entire component $C$ is reachable from $v$ but the whole component $C'$ is reachable as well. It means that DFS that is running from vertex $v$ will visit all vertices of components $C$ and $C'$, so they will be descendants for $v$ in a DFS tree. For each vertex $u\in C\cup C',\ u\ne v$ we have `tout[v] > tout[u]`.
	- If the component $C'$ was visited first, it means that DFS comes at some vertex $v\in C'$ at some moment, but all other vertices of components $C$ and $C'$ were not visited yet. By condition there is an edge $(C, C')$ in the condensation graph, so because of acyclic property, there is no back path from $C'$ to $C$, thus DFS from $v$ will not reach vertices of $C$. It means that vertices of $C$ will be visited by DFS later so `tout[C] > tout[C']`.
- If we sort all vertices $v\in V$ in decreasing order of their exit time `tout[v]` then the first vertex $u$ is going to belong to the **root** SCC and will have no incoming edges in the condensation graph. Now we run such search from $u$ so that it will visit all vertices in this SCC, but not others. Doing so, we can gradually select all SCC. Remove all vertices corresponding to the first SCC and find the vertex with the largest `tout`, and then run this search from it.
- Consider transposed graph $G^T$ which is received from $G$ by reversing the direction of each edge. This graph will have the same SCC as initial graph. Moreover, the condensation graph $G^\text{SCC}$ will also get transposed. Thus, there will be no edges from out root component to other components.
- For visiting the whole root SCC containing $v$, it is enough to run search from $v$ in graph $G^T$. This will visit all vertices of this SCC and only them. We can now proceed with the same method as done with $G$ but run it on $G^T$.
##### Implementation
1. Run sequence of DFS of $G$ which will return vertices with increasing tout, we store this in `order`.
2. Build $G^T$. Run series of DFS in the order determined by `order` (in decreasing order of tout). Every set of vertices, reached after the next search will be the next SCC.
We can compare the algorithm to that of [[Topological Sort]]. Step 1 of Kosaraju represents reversed topological sort of $G$. Also Kosaraju generates SCC by decreasing order of their exit times, thus it generates vertices of condensation graph in topological sort order.
```cpp
vector<bool> visited;
vector<int> order, component;
vector<vector<int>> adj, adj_rev;
int n;

void dfs1(int v)
{
	visited[v] = true;
	for (auto u : adj[v])
		if (!visited[u])
			dfs1(u);
	order.push_back(v); // order of vertices in reverse postorder
}

void dfs2(int v)
{
	visited[v] = true;
	component.push_back(v);
	for (auto u : adj_rev[v])
		if (!visited[u])
			dfs2(u);
}

int main()
{
	ios::sync_with_stdio(false);
	cin.tie(NULL);

	int m;
	cin >> n >> m;
	adj.resize(n);
	adj_rev.resize(n);
	for (int i = 0; i < m; i++)
	{
		int u, v;
		cin >> u >> v;
		adj[u - 1].emplace_back(v - 1);
		adj_rev[v - 1].emplace_back(u - 1);
	}

	visited.assign(n, false);
	order.clear();
	component.clear();

// First DFS to get the order of vertices in reverse postorder
	for (int i = 0; i < n; i++)
		if (!visited[i])
			dfs1(i);

	visited.assign(n, false);
	reverse(order.begin(), order.end());
	vector<vector<int>> scc;

// Second DFS to get the strongly connected components
	for (auto v : order)
		if (!visited[v])
		{
			dfs2(v);
			scc.push_back(component);
			component.clear();
		}

	return 0;
}
```
##### Condensation Graph Implementation
```cpp
	vector<int> roots(n, 0), root_nodes; // roots[i] is the root node to the scc to which i belongs
	vector<vector<int>> condensation(n);
// Second DFS to get the strongly connected components and condensation graph
	for (auto v : order)
		if (!visited[v])
		{
			dfs2(v);
			int root = component.front(); // root of the component
			for (auto u : component)
				roots[u] = root;
			root_nodes.push_back(root); // add the root to the list of roots
			scc.push_back(component);
			component.clear();
		}

// Construct the condensation graph
	for (int v = 0; v < n; v++)
		for (auto u : adj[v])
		{
			int root_v = roots[v], root_u = roots[u];
			if (root_v != root_u)
				condensation[root_v].push_back(root_u);
		}
```
Here, we select the root of each component as the first node in its list. This node will represent it's entire SCC in the condensation graph. `condensation` is the adjacency list of the `root_nodes`. We can traverse on `condensation` as our graph using only those nodes belonging to `root_nodes`.