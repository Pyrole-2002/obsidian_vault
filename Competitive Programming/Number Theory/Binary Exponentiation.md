Calculates $A^B$ in $O(\log B)$ instead of usual $O(B)$
- **Case 1:** If $B = 0$, result will always be $1$.
- **Case 2:** If $B$ is even, then we calculate $((A^2)^{\frac{B}{2}})$.
- **Case 3:** If $B$ is odd, then we calculate $(A \times (A^{\frac{B-1}{2}}) ^ 2)$.

```cpp
long long binpow(long long A, long long B) // recursive (slower)
{
    if (B == 0)
        return 1;
    long long res = binpow(A, B / 2);
    if (B & 1) // Odd
        return res * res * A;
    else
        return res * res;
}
```

```cpp
long long binpow(long long a, long long b) // iterative (faster)
{
    long long res = 1;
    while (b)
	{
        if (b & 1) 
			res = res * a;
        a = a * a;
        b >>= 1;
    }
    return res;
}
```

```cpp
long long binpow(long long a, long long b, long long m)
{
	a %= m;
	long long res = 1;
	while (b)
	{
		if (b & 1)
			res = res * a % m;
		a = a * a % m;
		b >>= 1;
	}
	return res;
}
```
## Applying A Permutation $k$ Times
Given a sequence of a certain length, apply a given permutation to it $k$ times.
To do this, raise the permutation to it's $k$-th power using binary exponentiation, and then apply it to the sequence. This takes a time complexity of $O(n\log k)$.
```cpp
vector<int> applyPermutation(vector<int> seq, vector<int> perm)
{
	vector<int> new_seq(seq.size());
	for (int i = 0; i < seq.size(); i++)
	{
		new_seq[i] = seq[perm[i]];
	}
	return new_seq;
}

vector<int> permute(vector<int> seq, vector<int> perm, long long k)
{
	while (k > 0)
	{
		if (k & 1)
		{
			seq = applyPermutation(seq, perm);
		}
		perm = applyPermutation(perm, perm);
		k >>= 1;
	}
	return seq;
}
```
> [!tip] This task can be solved more efficiently in linear time by building the permutation graph and considering each cycle independently. You could then compute $k$ modulo the size of the cycle and find the final position for each number which is part of this cycle.
## Multiplying Big Numbers Modulo $m$
When multiplying two big numbers, $a$ and $b$ modulo $m$, the product may be too big to fit in the data-type. So we use a variation of binary exponentiation to compute $a \cdot b \pmod m$.
$$
a\cdot b =
\begin{cases}
0 & \text{if $a=0$}\\
2\cdot\frac{a}{2}\cdot b & \text{if $a>0$ and $a$ even}\\
2\cdot\frac{a-1}{2}\cdot b + b & \text{if $a>0$ and $a$ odd}
\end{cases}
$$
