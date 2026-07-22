- A graph is a non-linear data structure consisting of nodes that have data and are connected to other nodes through edges.
![[Pasted image 20240613032827.png|left|400]]          ![[Pasted image 20240613032852.png|right|300]]          ![[Pasted image 20240613132654.png]]
- Nodes (vertices) are circles represented by numbers. The numbering can be done in any order, no specific order needs to be followed.
- An undirected graph is a graph where edges are bidirectional, with no direction associated with them. Pair of vertices to represent any edge is unordered.
- A directed graph is a graph where all the edges are directed from one node to another. Pair of vertices to represent an edge is ordered.
- A graph is called cyclic if we can start traversal from a node and end at the same node.
###### Degree of Graph
- It is the number of edges that go inside or outside that node.
- For undirected graphs, the degree of a node is the number of edges attached to that node.
- For directed graphs, indegree is the number of incoming edges and outdegree is the number of outgoing edges.
- Total degree of a graph is twice to number of edges.
### Representation
The two most commonly used representations for graphs are:
- Adjacency Matrix
- Adjacency List
#### Adjacency Matrix
An adjacency matrix of a graph is a 2-D array of size $n\times n$, where $n$ is the number of nodes in the graph. Here `adj[i][j] = 1` if the edge $(v_{i},v_{j})$ is in the set of edge and `adj[i][j] = 0` if there is no such edge.
```cpp
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(NULL);

	int n, m;
	cin >> n >> m;
	// adjacency matrix
	int adj[n+1][n+1];
	for (int i = 0; i < m; i++)
	{
		int u, v;
		cin >> u >> v;
		adj[u][v] = 1;
		adj[v][u] = 1; // remove this line if the graph is directed
	}

		return 0;
}
```
- Time Complexity: $O(V)$
- Space Complexity: $O(V^2)$
- For weighted graphs, instead of updating the adjacency matrix value to $1$, we update it to the corresponding weight of the edge `adj[u][v] = wt`.
#### Adjacency List
An adjacency list takes very less space compared to adjacency matrix. We associate with each node a list of nodes adjacent to it. We create an array of [[Vector]] of integers.
For directed graphs, if there is an edge between $u$ and $v$, it means the edge only goes from $u$ to $v$. This means $v$ is the neighbor of $u$ but vice versa is not true.
```cpp
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(NULL);

	int n, m;
	cin >> n >> m;
	// adjacency list
	vector<int> adj[n + 1];
	for (int i = 0; i < m; i++)
	{
		int u, v;
		cin >> u >> v;
		adj[u].emplace_back(v);
		adj[v].emplace_back(u); // remove this line if the graph is directed
	}

		return 0;
}
```
- Space Complexity: $O(2\times E)$ for undirected graphs and $O(E)$ for directed graphs.
- For weighted graphs, instead of storing just the node number, we store pairs of node and weight, `vector<pair<int, int>> adj[n + 1]`.