- A strong orientation of an undirected graph is an assignment of a direction to each edge that makes it a [[Connected Components#Kosaraju Algorithm|Strongly Connected Component]].
- This cannot be done to every graph. For example, a graph containing a [[Bridges & Articulation Points|Bridge]] can't be strongly oriented as it will become crossable in only one direction.
- Consider a [[DFS]] through a bridgeless connected graph. Clearly, we will each vertex. Since there are no bridges, we can remove any DFS tree edge and still be able to go from below the edge to above the edge by using a path that contains at least one back edge. Therefore, from any vertex, we can go to the root of the DFS tree. Also, from the root of the DFS tree, we can visit any vertex.
- To strongly orient a bridgeless connected graph, run a DFS on it and let the DFS tree edges point away from the DFS root and all other edges from the descendant to the ancestor in the DFS tree.
- The result that bridgeless connected graphs are exactly the graphs that have strong orientations is called **Robbins' Theorem**.
### Extension
- Consider an extension of the orientation problem, where we need to find a graph orientation so that the number of SCC's is minimal.
- Each graph component will be considered separately. Since only bridgeless graphs are strongly orientable, let's remove all bridges temporarily. We end up with some number of bridgeless components (components at beginning + number of bridges) and each can be strongly oriented.
- Since we were only allowed to orient edges and not remove them, we can orient the bridges arbitrarily.
#### Implementation
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<pair<int, int>> edges;
vector<bool> edge_used;
vector<int> tin, low;
int timer;
string orient;
int bridge_cnt;

void findBridges(int v)
{
	timer = 0;
	low[v] = tin[v] = timer++;
	for (auto p : adj[v])
	{
		if (edge_used[p.second])
			continue;
		edge_used[p.second] = true;
		orient[p.second] = (v == edges[p.second].first) ? '>' : '<'; // dir is from v to nv
		int nv = p.first; // neighbour vertex
		if (tin[nv] == -1)
		{
			findBridges(nv);
			low[v] = min(low[v], low[nv]);
			if (low[nv] > tin[v]) // bridge between v and nv
				bridge_cnt++;
		}
		else
			low[v] = min(low[v], tin[nv]);
	}
}

int main()
{
	ios::sync_with_stdio(false);
	cin.tie(NULL);

	int m;
	cin >> n >> m;
	adj.resize(n);
	for (int i = 0; i < m; i++)
	{
		int u, v;
		cin >> u >> v;
		adj[u - 1].emplace_back(v - 1, i);
		adj[v - 1].emplace_back(u - 1, i);
		edges.emplace_back(u - 1, v - 1);
	}

	tin.assign(n, -1);
	low.assign(n, -1);
	edge_used.assign(m, false);
	orient.resize(m);
	bridge_cnt = 0;

	int num_components = 0;
	for (int v = 0; v < n; v++)
		if (tin[v] == -1)
		{
			findBridges(v);
			num_components++;
		}

	return 0;
}

```