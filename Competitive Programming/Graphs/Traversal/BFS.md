Breadth First Search is a basic searching algorithm on [[Graphs Representation|Graphs]].
- The path found by BFS to any node is the shortest path to that node, i.e. the path that contains the smallest number of edges in an unweighted graph.
- Time Complexity: $O(V+E)$, where $V$ is the number of vertices and $E$ is the number of edges.
- Space Complexity: $O(V)$
- The algorithm takes as input an unweighted graph and the source vertex $s$. The graph may be directed or undirected.
- Create a queue $q$ which will contain the vertices to be processed and a boolean array `used[]` which indicates for each vertex if it has been visited or not.
- Initially, push the source $s$ to the queue and set `used[s] = true`, and for all other vertices $v$ set `used[v] = false`.
- Then loop until the queue is empty and in each iteration, pop a vertex from the queue. Iterate through all the edges going out of this vertex and if some of these edges go to vertices that are not already visited, mark them as visited and place them in the queue.
- When the queue becomes empty, we would have traversed all vertices reachable from source $s$, with each vertex reached in the shortest possible way.
- We can also calculate the lengths of the shortest paths in `d[]` and to retrieve these shortest paths we maintain `p[]` which stores the parent of each vertex in that path.
```cpp
pair<vector<int>, vector<int>> BFS(vector<vector<int>> adj, int n, int s)
{
	queue<int> q;
	vector<bool> visited(n, false);
	vector<int> d(n, 0), p(n, -1);

	q.push(s);
	visited[s] = true;
	while (!q.empty())
	{
		int u = q.front();
		q.pop();
		for (int v : adj[u])
		{
			if (!visited[v])
			{
				visited[v] = true;
				d[v] = d[u] + 1;
				p[v] = u;
				q.push(v);
			}
		}
	}
	return {d, p};
}
```
- To display the shortest path from source to vertex, first perform BFS and retrieve the parent array, use to print the shortest path:
```cpp
void printPath(vector<int> p, int u)
{
	if (u == -1)
		return;
	printPath(p, p[u]);
	cout << u << " ";
}
```