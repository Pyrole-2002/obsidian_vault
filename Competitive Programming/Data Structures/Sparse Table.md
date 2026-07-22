- Sparse table is a data structure that allows answering range queries.
- It can answer most range queries in $O(\log n)$, but its true power is answering range minimum queries (or equivalent range maximum queries) in $O(1)$ time.
- Only drawback is that it can be used only on immutable arrays. The array cannot be changed between two queries. If any element in the array changes, the complete data structure has to be recomputed.
- Just like binary numbers, any non-negative number can be represented uniquely as a sum of decreasing powers of $2$ (these powers are called summands). For a number $x$, there can be at most $\lceil\log_{2}x\rceil$.
- By the same logic, any interval can be uniquely represented as a union of intervals with lengths that are decreasing powers of $2$. The union consists of at most $\lceil\log_{2}(\text{len})\rceil$ intervals, where $\text{len}$ is the length of the original interval.
## Precomputation
- We use a 2-dimensional array for storing the answers to the precomputed queries.
- `st[i][j]` will store the answer for the range $[j, j+2^i-1]$ of length $2^i$.
- The size of the 2-dimensional array will be $(K+1)\times\text{MAXN}$.
- $\text{MAXN}$ is the biggest possible array length we want to handle.
- $K$ has to satisfy $K \ge \lfloor\log_{2}\text{MAXN}\rfloor$, because $2^{\lfloor\log_{2}\text{MAXN}\rfloor}$ is the biggest power of two range, that we have to support.
- For arrays with length $\le 10^7$ elements, $K=25$ is a good value.
- The $\text{MAXN}$ dimension is second to allow cache friendly consecutive memory access.
```cpp
int st[K + 1][MAXN];
```
- Because the range $[j, j+2^i-1]$ of length $2^i$ splits nicely into the ranges $[j, j+2^{i-1}-1]$ and $[j+2^{i-1}, j+2^i-1]$, both of length $2^{i-1}$,  we can generate the table efficiently using dynamic programming:
```cpp
// copy all elements from array into first row of sparse table
copy(array.begin(), array.end(), st[0]); // intervals of length 1

for (int i = 1; i <= K; i++)
	for (int j = 0; j + (1<<i) <= N; j++)
		st[i][j] = f(st[i-1][j], st[i-1][j+(1<<(i-1))]);
```
The function $f$ will depend on the type of query. For range sum queries, it will compute the sum. For range minimum queries, it will compute the minimum.
- Time Complexity: $O(N\log N)$
### Range Sum Queries
- We want to find the sum of all values in a range.
- $f(x, y) = x + y$, where $f$ is the precomputation function.
- To answer the sum query for the range $[L, R]$, we iterate over all powers of $2$, starting from the biggest one. As soon as $2^i$ is found which is smaller or equal to the length of the range $(R-L+1)$, we process the first part of range $[L, L+2^i-1]$ and continue with the remaining range $[L+2^i, R]$
```cpp
#define MAXN 1000000
#define K 25
long long st[K + 1][MAXN];

long long f(long long x, long long y)
{
	return x + y;
}

void precompute(vector<int> array)
{
	int N = array.size();
	copy(array.begin(), array.end(), st[0]);

	for (int i = 1; i <= K; i++)
		for (int j = 0; j + (1<<i) <= N; j++)
			st[i][j] = f(st[i-1][j], st[i-1][j+(1<<(i-1))]);
}

long long query(int L, int R)
{
	long long sum = 0;
	for (int i = K; i >= 0; i--)
	{
		if ((1<<i) <= R - L + 1)
		{
			sum = f(sum, st[i][L]);
			L += 1<<i;
		}
	}
	return sum;
}
```
- Time Complexity: $O(K)$ or $O(\log\text{MAXN})$
### Range Minimum Queries (RMQ)
- When computing the minimum of a range, it doesn't matter if we process a value in the range once or twice.
- Therefore, instead of splitting a range into multiple ranges, we split the range into only two overlapping ranges.
- The length of these two overlapping ranges will be equal to $\lfloor\log_{2}(R-L+1)\rfloor$.
- Refer to the [[Tricks#Logarithm in Constant Time|Logarithm in Constant Time]] to calculate logarithm quickly.
- During precomputation: $f(x, y) = \text{min}(x, y)$, where $f$ is the precomputation function.
```cpp
#define MAXN 1000000
#define K 25
long long st[K + 1][MAXN];

// pre C++20
int log2_floor(unsigned long long i)
{
	return i ? __builtin_clzll(1) - __builtin_clzll(i) : -1;
}

long long f(long long x, long long y)
{
	return min(x, y);
}

void precompute(vector<int> array)
{
	int N = array.size();
	copy(array.begin(), array.end(), st[0]);

	for (int i = 1; i <= K; i++)
		for (int j = 0; j + (1<<i) <= N; j++)
			st[i][j] = f(st[i-1][j], st[i-1][j+(1<<(i-1))]);
}

long long query(int L, int R)
{
    int i = log2_floor(R - L + 1);
    return f(st[i][L], st[i][R - (1 << i) + 1]);
}
```
- Time Complexity: $O(1)$