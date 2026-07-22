- Given a weighted [[Graphs Representation|graph]] $G$ with $n$ vertices, we will find the length of the shortest path $d_{ij}$ between each pair of vertices $i$ and $j$.
- The graph can have negative weights but no negative cycles should exist. This algorithm can be used to detect the presence of negative cycles. The graph has a negative cycle if at the end of the algorithm, the distance from a vertex $v$ to itself is negative.
- The idea is to partition the process of finding the shortest path between any two vertices to several incremental phases.
- The matrix of distances is $d$ and the vertices are numbered from $1$ to $n$.
- Before $k^\text{th}$ phase, $d[i][j]$ for any vertices $i$ and $j$ stores the length of the shortest path between the vertex $i$ and $j$ which contains only the vertices $\{1,2,\dots,k-1\}$ as internal vertices in the path (beginning and end excluded). In other words, before $k^\text{th}$ phase, the value of $d[i][j]$ is equal to the length of the shortest path from $i$ to $j$, if this path is allowed to enter only the vertex with numbers smaller than $k$ (the beginning and end of the path are not restricted by this property).
- For $k=0$, we can fill matrix with $d[i][j]=w_{ij}$ if there exists an edge between $i$ and $j$ with weight $w_{ij}$ and $d[i][j] = \infty$ otherwise.
- In the subsequent phases, we fix the distances for some vertex pairs $(i, j)$:
	- If the shortest way from $i$ to $j$ with internal vertices in $\{1,\dots, k\}$ coincides with the shortest path with internal vertices in $\{1,\dots, k-1\}$. In this case $d[i][j]$ will not change in this phase.
	- If the shortest path with internal vertices in $\{1,\dots, k\}$ is shorter. This means that the new shorter path passes through the vertex $k$ and we can split the shortest path between $i$ and $j$ into two paths: the path between $i$ and $k$, and the path between $k$ and $j$. Both these paths only use internal vertices in $\{1,\dots, k-1\}$ and are the shortest such paths in that respect. Therefore, we already have computed the lengths of those paths before, and we can compute the length of the shortest path between $i$ and $j$ as $d[i][k]+d[k][j]$.
- Combining these two cases, to recalculate the length of all pairs $(i, j)$ in the $k^\text{th}$ phase:
$$
d_{\text{new}}[i][j] = \min(d[i][j],\ d[i][k]+d[k][j])
$$
- The time complexity of this algorithm is $O(V^3)$.
```cpp
int n;
vector<vector<pair<int, int>>> adj;
vector<vector<int>> d, p;

void floydWarshall()
{
	for (int i = 0; i < n; i++)
	{
		d[i][i] = 0;
		for (auto u : adj[i])
		{
			d[i][u.first] = u.second;
			p[i][u.first] = i;
		}
	}
	for (int k = 0; k < n; k++)
		for (int i = 0; i < n; i++)
			for (int j = 0; j < n; j++)
				if (d[i][k] < INF && d[k][j] < INF && d[i][j] > d[i][k] + d[k][j])
				{
					d[i][j] = d[i][k] + d[k][j];
					p[i][j] = p[k][j];
				}
}
```
### Negative Cycles
- The algorithm does not apply to graphs containing negative cycles. But for all pairs of vertex $i$ and $j$ for which there doesn't exist a path starting at $i$, visiting a negative cycle, and ending at $j$, the algorithm will work correctly.
- To handle the other cases, we run the algorithm for a given graph. Then a shortest path between $i$ and $j$ does not exist iff there is a vertex $t$ that is reachable from $i$ and also from $j$ for which $d[t][t]<0$.