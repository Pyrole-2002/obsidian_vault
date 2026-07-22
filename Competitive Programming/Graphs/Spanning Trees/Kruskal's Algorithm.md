- Given a weighted undirected [[Graphs Representation|graph]] $G$ with $V$ vertices and $E$ edges, we will find a spanning tree of this graph which connects all vertices and has the least sum of weights of all edges. A spanning tree is a set of edges such that any vertex can reach any other by exactly one simple path. The spanning tree with the least weight is called a Minimum Spanning Tree.
![[Pasted image 20240703014427.png]]![[Pasted image 20240703014433.png]]
- Kruskal's Algorithm initially places all the nodes of the original graph isolated from each other to form a forest of single node trees and then gradually merges these trees combining at each iteration any two of all the trees with some edge of the original graph.
- Before the execution of the algorithm, all edges are sorted by weight (non-decreasing).
- During unification, pick all edges from the first to the last (in sorted order), and if the ends of the currently picked edge belong to different subtrees, combine the subtrees using the edge.
- After iterating through all edges, all vertices will belong to the same sub-tree and we will get the answer.
### Simplest Implementation
Directly implementing the algorithm results in $O(E\log E+V^2)$ time complexity.
```cpp
int n, m;
vector<Edge> adj;

struct Edge
{
	int u, v, w;
};

vector<Edge> kruskal()
{
	vector<Edge> mst;
	sort(adj.begin(), adj.end(), [](Edge a, Edge b) { return a.w < b.w; });
	vector<int> tree_id(n); // for each vertex, store the tree it belongs to
	for (int i = 0; i < n; i++)
		tree_id[i] = i; // initially each vertex is in its own tree
	for (Edge e : adj)
		if (tree_id[e.u] != tree_id[e.v])
		{
			mst.push_back(e);
			int old_id = tree_id[e.u], new_id = tree_id[e.v];
			for (int i = 0; i < n; i++)
				if (tree_id[i] == old_id)
					tree_id[i] = new_id;
		}

	return mst;
}
```
## Using [[Disjoint Set Union]]
We can use DSU for implementing Kruskal's Algorithm which will achieve the time complexity of $O(E\log V)$.
```cpp
int n, m;
vector<Edge> adj;
vector<int> parent, rank;

void make_set(int v)
{
	parent[v] = v;
	rank[v] = 0;
}

int find_set(int v)
{
	if (v == parent[v])
		return v;
	return parent[v] = find_set(parent[v]);
}

void union_sets(int a, int b)
{
	a = find_set(a);
	b = find_set(b);
	if (a != b)
	{
		if (rank[a] < rank[b])
			swap(a, b);
		parent[b] = a;
		if (rank[a] == rank[b])
			rank[a]++;
	}
}

struct Edge
{
	int u, v, w;
	bool operator<(Edge const& other)
	{
		return w < other.w;
	}
};

vector<Edge> kruskal()
{
	vector<Edge> mst;
	sort(adj.begin(), adj.end());
	for (int i = 0; i < n; i++)
		make_set(i);
	for (Edge e : adj)
		if (find_set(e.u) != find_set(e.v))
		{
			mst.push_back(e);
			union_sets(e.u, e.v);
		}

	return mst;
}
```