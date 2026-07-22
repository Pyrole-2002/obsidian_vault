Depth First Search is a basic searching algorithm on [[Graphs Representation|Graphs]].
- DFS finds the lexicographical first path in the graph from a source vertex $s$ to each vertex.
- Time Complexity: $O(V+E)$
- Space Complexity: $O(V)$
- The idea is to go as deep into the graph as possible and backtrack once we are at a vertex without any unvisited adjacent vertices.
- Below is the simplest implementation of DFS:
```cpp
vector<bool> visited;

void DFS(vector<vector<int>> adj, int vertex)
{
	visited[vertex] = true;
	for (int i : adj[vertex])
		if (!visited[i])
			DFS(adj, i);
}
```
### Classification of Edges
We classify the edges of a graph $G$, using the entry and exit time of the end nodes $u$ and $v$ of the edge $(u,v)$.
We perform DFS and classify the encountered edges using the following rules:
##### If $v$ is not visited
- **Tree Edge:** If $v$ is visited after $u$ then edge $(u, v)$ is called a tree edge. In other words, if $v$ is visited for the first time and $u$ is currently being visited then $(u, v)$ is called tree edge. These edges form a DFS tree and hence the name tree edges.
##### If $v$ is visited before $u$
- **Back Edge:** If $v$ is an ancestor of $u$, then the edge $(u, v)$ is a back edge. $v$ is an ancestor exactly if we already entered $v$, but not exited it yet. Back edges complete a cycle as there is a path from ancestor $v$ to descendant $u$ (in the recursion of DFS) and an edge from descendant $u$ to ancestor $v$ (back edge). Cycles can be detected using back edges.
- **Forward Edge:** If $v$ is a descendant of $u$, then edge $(u, v)$ is a forward edge. In other words, if we already visited and exited $v$ and `entry[u] < entry[v]` then the edge $(u, v)$ forms a forward edge.
- **Cross Edge:** If $v$ is neither an ancestor nor descendant of $u$, then edge $(u, v)$ is a cross edge. In other words, if we already visited and exited $v$ and `entry[u] > entry[v]` then edge $(u, v)$ is a cross edge.
> [!tip] Let $G$ be an undirected Graph. Then, performing a DFS upon $G$ will classify every encountered edge as either a tree edge or back edge, forward and cross edges only exist in directed graphs.
- It is often useful to compute the entry and exit times and vertex color. We color all vertices with the color $0$ if we haven't visited them, with the color $1$ if we visited them, and with the color $2$ if we already exited the vertex.
```cpp
vector<int> color, time_in, time_out;
int timer = 0;

void DFS(vector<vector<int>> adj, int vertex)
{
	time_in[vertex] = timer++;
	color[vertex] = 1; // entering
	for (int u : adj[vertex])
		if (color[u] == 0) // not visited
			DFS(adj, u);
	color[vertex] = 2; // exiting
	time_out[vertex] = timer++;
}
```
- Remember to resize the global vectors before calling the DFS function.