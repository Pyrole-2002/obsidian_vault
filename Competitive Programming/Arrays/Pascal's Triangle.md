![[Pasted image 20240607182351.png]]
In the Pascal's Triangle, the $k^\text{th}$ entry in the $n^\text{th}$ row is denoted by $\binom{n}{k}$ ($1$ indexed).
We can see the downward construction of the Pascal's Triangle using the formula:
$$
\binom{n}{k} = \binom{n-1}{k-1} + \binom{n-1}{k}
$$
where $0\le k\le n$. This is known as **Pascal's Rule**.
Classic formula:
$$
\binom{n}{r} = \frac{n!}{r!\cdot (n-r)!}
$$
Optimized formula:
$$
\binom{n}{r} = \frac{n\cdot(n-1)\cdot(n-2)\dots(n-r+1)}{r\cdot(r-1)\cdot(r-2)\dots 1} = \frac{n}{1}\cdot\frac{n-1}{2}\cdot\frac{n-2}{3}\cdots\frac{n-r+1}{r}
$$
```cpp
int nCr(int n, int r)
{
	long long res = 1;
	for (int i = 0; i < r; i++)
	{
		res *= n - i;
		res /= i + 1;
	}
	return res;
}
```
## Finding an Element
> [!tip] Given the row number $r$ and column number $c$, return the corresponding element in Pascal's Triangle.

```cpp
int pascalTriangleElement(int r, int c)
{
	return nCr(r - 1, c - 1;)
}
```
Time Complexity: $O(c)$ where $c$ is column number.
Space Complexity: $O(1)$
## Finding a Row
> [!tip] Given the row number $r$, return the corresponding row in Pascal's Triangle.

```cpp
vector<int> pascalTriangleRow(int r)
{
	vector<int> ans(r);
	long long temp = 1;
	ans[0] = temp;
	for (int i = 1; i < r; i++)
	{
		temp *= n - i;
		temp /= i;
		ans[i] = temp;
	}
	return ans;
}
```
Time Complexity: $O(r)$ where $r$ is row number.
Space Complexity: $O(1)$

## Finding the Entire Triangle
> [!tip] Given the row number $r$, return the corresponding Pascal's Triangle up till that row.

```cpp
vector<vector<int>> pascalTriangle(int n)
{
	vector<vector<int>> ans;
	for (int row = 1; row <= n; row+)
	{
		ans.emplace_back(pascalTriangleRow(row));
	}
	return ans;
}
```
Time Complexity: $O(n^2)$
Space Complexity: $O(1)$