- Disjoint Set Union is also known as DSU or Union Find.
- We are given several elements, each of which is a separate set. A DSU will have an operation to combine any two sets, and it will be able to tell in which set a specific element is.
- Thus, the basic interface of this data structure consists of three operations:
	- `make_set(v)` : creates a new set consisting of the new element $v$.
	- `union_sets(a, b)` : merges the set which contains element $a$ and the set which contains the element $b$.
	- `find_set(v)` : returns the representative (leader) of the set that contains the element $v$. This leader is an element of the corresponding set. It is selected in each set by the data structure itself and may change after `union_sets(a, b)` is called. This leader can be used to check if two elements are part of the same set or not. $a$ and $b$ are in the exact same set if `find_set(a) == find_set(b)`. Otherwise, they are in different sets.
- The DSU data structure allows us to do each of these operations in almost $O(1)$ time.
- There also exists an alternative structure of DSU which has a slower average complexity of $O(\log n)$, but can be more powerful than the regular DSU.
In the following image, you can see the representation of such trees:
![[Pasted image 20240601234100.png|300]]![[Pasted image 20240601234232.png|300]]![[Pasted image 20240601235429.png|200]]
In the beginning, every element starts as a singleton set, each vertex is its own tree. Then we combine the set containing element $0$ and the set containing element $1$. Similarly, we combine sets containing elements $2$ and element $3$. Lastly, we combine the set containing element $0$ and element $2$.
- For the implementation, we will have to maintain an array `parent` that stores a reference to its immediate ancestor in the tree.
# Naive Implementation
- To create a new set `make_set(v)`, we simply create a tree with root as the vertex $v$, which becomes it's own ancestor.
- To combine two sets `union_sets(a, b)`, we first find the representative of the set in which $a$ is located, and the representative of the set in which $b$ is located. If the representatives are identical, we have nothing to do and the sets are already merged. Otherwise, we simply specify that one of the representatives is the parent of the other representative, combining the two trees.
- For finding the representative `find_set(v)`, we simply climb the ancestors of the vertex $v$ until we reach the root (reference to the ancestor leads to itself) using recursion.
```cpp
vector<int> parent;

void make_set(int v)
{
	parent[v] = v;
}

int find_set(int v)
{
	if (v == parent[v])
		return v;
	return find_set(parent[v]);
}

void union_sets(int a, int b)
{
	a = find_set(a);
	b = find_set(b);
	if (a != b)
		parent[b] = a;
}

int main()
{
	ios::sync_with_stdio(false);
	cin.tie(NULL);

	int n = 5; // number of vertices
	parent.resize(n);
	for (int i = 0; i < n; i++)
		make_set(i);

	union_sets(0, 1);
	union_sets(2, 3);
	union_sets(0, 4);
	cout << find_set(0) << endl;
	cout << find_set(1) << endl;
	cout << find_set(2) << endl;
	cout << find_set(3) << endl;
	cout << find_set(4) << endl;

	return 0;
}
```
- However, this implementation is inefficient because the operations may take $O(n)$ time.
## Path Compression
- This optimization speeds up `find_set`.
- If we call `find_set(v)` for some vertex $v$, we actually find the leader $p$ for all vertices that we visit on the path between $v$ and the leader $p$.
- We will therefore make the paths for all nodes shorter, by setting the parent of each visited vertex directly to $p$.
In the below image, on the left is a tree, and on the right is the compressed tree after calling `find_set(7)` which shortens the path for all visited nodes $7\to{5}\to{3}\to{2}\to{1}$.
![[Pasted image 20240602193845.png|400]]![[Pasted image 20240602194242.png]]
```cpp
int find_set(int v)
{
	if (v == parent[v])
		return v;
	return parent[v] = find_set(parent[v]);
}
```
- This modification achieves the time complexity $O(\log n)$ per call on average.
## Union by rank
- In this optimization, we change the `union_set` operation.
- We change which tree gets attaches to the other one. In the naive implementation, the second tree always got attached to the first one. This can lead to trees containing chains of length $O(n)$.
- We popularly use two heuristics:
	1. Using the size of the trees as rank.
	2. Using the depth of the tree as rank.
- We attach the tree with the lower rank to the one with the higher rank.
###### Union By Size
```cpp
void make_set(int v)
{
	parent[v] = v;
	size[v] = 1;
}

void union_sets(int a, int b)
{
	a = find_set(a);
	b = find_set(b);
	if (a != b)
	{
		if (size[a] < size[b])
			swap(a, b);
		parent[b] = a;
		size[a] += size[b]; // update only for leader
	}
}
```
###### Union by Depth of Tree
```cpp
void make_set(int v)
{
	parent[v] = v;
	rank[v] = 0;
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
			rank[a]++; // update only for leader
	}
}
```
- If we only use union by rank without path compression, operations work in nearly $O(\log n)$.
- If we combine both path compression and union by rank, we will reach nearly constant time operations.
- Time Complexity is $O(\alpha(n))$ on average, where $\alpha(n)$ is the Inverse Ackermann Function, which grows very slowly. It doesn't exceed $4$ for approximately $n < 10^{600}$.
# Applications
### Connected Components in a Graph
- DSU can be used when we have an empty graph, and want to add vertices and undirected edges to answer the queries whether two vertices are in the same connected component of the graph or not.
- Nearly the same problem appears in [[Kruskal's Algorithm]].
### Search for Connected Components in an Image
- Let there be an image of $n\times m$ pixels. Initially, all are white, but then few black pixels are drawn. Determine the size of each white connected component in the final image.
- Iterate over all white pixels in the image, for each pixel, iterate over it's four neighbors and if the neighbor is white call `union_sets`.
- We will now have a DSU with $n\cdot m$ nodes corresponding to image pixels.
- The resulting trees in the DSU are the desired connected components.
### Painting Subarrays Offline
- Let there be a set of vertices, and each vertex has an outgoing edge to another vertex. With DSU, we can find the end point to which we get after following all edges from a given starting point in almost constant time.
- The problem of painting subarrays assumes a segment of length $L$, each element initially has the color $0$. We have to repaint the subarray $[l, r]$ with the color $c$ for each query $(l, r, c)$. At the end, we want to find the final color of each cell. Assume that we know all queries in advance (offline task).
- For the solution, we make a DSU, which for each cell stores a link to the next unpainted cell. Therefore, initially each cell points to itself.
- After painting one requested repaint of a segment, all cells from that segment will point to the cell after the segment.
- Consider the queries in reverse order. This way when we execute a query, we only have to paint exactly the unpainted cells in the subarray $[l, r]$. All other cells already contain their final color.
- To quickly iterate over all unpainted cells, we use the DSU. We find the left-most unpainted cell inside of a segment, repaint it, and with the pointer we move to the next empty cell to the right.
- We use DSU with path compression, but we cannot use union by rank because it is important who becomes the leader after union. Time complexity will be $O(\log n)$ per union.
```cpp
for (int i = 0; i <= L; i++)
	make_set(i);
for (int i = m-1; i >= 0; i--)
{
	int l = query[i].l;
	int r = query[i].r;
	int c = query[i].c;
	for (int v = find_set(l); v <= r; v = find_set(v))
	{
		answer[v] = c;
		parent[v] = v + 1; 
	}
}
```
- However, we can use union by rank, if we store the next unpainted cell in an additional array `end[]`. Then we can merge two sets into one ranked according to their heuristics and obtain the solution in$O(\alpha(n))$.
### Support Distances up to Representative
- If we want to maintain the distance between a vertex and the representative of its set, without path compression, the distance is just the number of recursive calls. This will be inefficient.
- To do path compression, we store the distance to the leader as additional information for each node.
- We use an array of pairs for `parent[]` and the function `find_set` now returns two numbers, the leader of the set and the distance to it.
```cpp
void make_set(int v)
{
	parent[v] = make_pair(v, 0);
	rank[v] = 0;
}

pair<int, int> find_set(int v)
{
	if (v != parent[v].first)
	{
		int len = parent[v].second;
		parent[v] = find_set(parent[v].first);
		parent[v].second += len;
	}
	return parent[v];
}

void union_sets(int a, int b)
{
	a = find_set(a).first;
	b = find_set(b).first;
	if (a != b)
	{
		if (rank[a] < rank[b])
			swap(a, b);
		parent[b] = make_pair(a, 1);
		if (rank[a] == rank[b])
			rank[a]++;
	}
}
```
### Support the Parity of Path Length / Checking Bipartiteness Online
- In the same way as computing path length to leader, it is possible to maintain the parity of the length of the path before it.
- Lets say we have an empty graph, it can have edges added to it and we have to answer the queries whether the connected component containing a certain vertex is bipartite.
- To solve this, we make a DSU for storing of the components and store the parity of the path up to the leader for each vertex.
- Thus, we can quickly check if adding an edge leads to a violation of the bipartiteness or not. If the ends of the edge lie in the same connected component and have the same parity length to the leader, then adding this edge will produce a cycle of odd length, and the component will lose the bipartiteness property.
- If we add an edge $(a, b)$ that connected two connected components into one, then when we attach one tree to another, we need to adjust the parity.
- We can create a formula which computes the parity issued to the leader of the set that will get attached to another set. Let $x$ be the parity of the path length from vertex $a$ up to its leader $A$, and $y$ be the parity of the path length from vertex $b$ up to its leader $B$, and $t$ be the desired parity that we have to assign to $B$ after the merge.
- Regardless of how many joins we perform, the parity of edges is carried from one leader to another. The path contains three parts:
	1. From $B$ to $b$
	2. From $b$ to $a$, which is connected by one edge and has parity $1$
	3. From $a$ to $A$
$$
t = x \oplus y\oplus 1
$$
- Here we also store in the array `bipartite[]` whether it is still bipartite or not.
```cpp
void make_set(int v)
{
	parent[v] = make_pair(v, 0);
	rank[v] = 0;
	bipartite[v] = true;
}

pair<int, int> find_set(int v)
{
	if (v != parent[v].first)
	{
		int parity = parent[v].second;
		parent[v] = find_set(parent[v].first);
		parent[v].second ^= parity;
	}
	return parent[v];
}

void add_edge(int a, int b)
{
	pair<int, int> pa = find_set(a);
	a = pa.first;
	int x = pa.second;

	pair<int, int> pb = find_set(b);
	b = pb.first;
	int y = pb.second;

	if (a == b)
	{
		if (x == y)
			bipartite[a] = false;
	}
	else
	{
		if (rank[a] < rank[b])
			swap(a, b);
		parent[b] = make_pair(a, x^y^1);
		bipartite[a] &= bipartite[b];
		if (rank[a] == rank[b]);
			rank[a]++;
	}
}

bool is_bipartite(int v)
{
	return bipartite[find_set(v).first];
}
```
### Offline RMQ / Arpa's Trick
- We are given an array `a[]` and we have to compute some minima in given segments of the array.
- We iterate over the array and when we are at the $i^\text{th}$ element, we will answer all queries $(L, R)$ where $R = i$.
- We will keep a DSU using the first $i$ elements. The parent of an element is the next smaller element to the right of it.
- Then using this structure, the answer to a query will be the `a[find_set(L)]`, the smallest number to the right of $L$.
- We need to know all the queries beforehand (offline).
- `container[i]` contains all queries with $R = i$.
```cpp
struct Query
{
	int L, R, idx;
};
vector<int> answer;
vector<vector<Query>> container;
```
- This is know as Arpa's Trick.
```cpp
stack<int> s;
for (int i = 0; i < n; i++)
{
	while (!s.empty() && a[s.top()] > a[i])
	{
		parent[s.top()] = i;
		s.pop();
	}
	s.push(i);
	for (Query q : container[i])
		answer[q.idx] = a[find_set(q.L)];
}
```
### Storing the DSU in a Set List
- An alternative way of storing the DSU is preservation of each set in the form of an explicitly stores list of its elements. At the same time, each element also stores the reference to the representative of its set.
- The use of a weighting heuristic can significantly reduce the asymptotic complexity to $O(m+n\log n)$ to perform $m$ queries on the $n$ elements.
- For the weighting heuristic, we will always add the smaller set to the bigger set.
- Adding one set to another takes time proportional to the size of the added set. Search for the leader takes $O(1)$ with this method of storing.
```cpp
vector<int> lst[MAXN];
int parent[MAXN];

void make_set(int v)
{
	lst[v] = vector<int> (1, v);
	parent[v] = v;
}

int find_set(int v)
{
	return parent[v];
}

void union_sets(int a, int v)
{
	a = find_set(a);
	b = find_set(b);
	if (a != b)
	{
		if (lst[a].size() < lst[b].size())
			swap(a, b);
		while (!lst[b].empty())
		{
			int v = lst[b].back();
			lst[b].pop_back();
			parent[v] = a;
			lst[a].emplace_back(v);
		}
	}
}
```