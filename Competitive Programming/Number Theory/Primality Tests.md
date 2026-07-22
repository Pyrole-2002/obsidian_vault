Primality test algorithms determine if a number $n$ is prime or not.
## Trial Division
- A composite number has at least one additional divisor other than $1$ and $n$ (the number itself), let's call this this divisor $d$.
- This means that $\frac{n}{d}$ is also a divisor of $n$.
- Either $d$ or $\frac{n}{d}$ is $\le \sqrt n$.
- We find a non-trivial divisor by checking if any of the numbers between $2$ and $\sqrt n$ is a divisor of $n$.
- Time complexity is $O(\sqrt n)$.
```cpp
bool isPrimeTrial(int n)
{
	for (int d = 2; d*d <= n; d++)
	{
		if (n % d == 0)
			return false;
	}
	return x >= 2;
}
```
## Fermat Primality Test
- This is a probabilistic test based on [[Fermat's Little Theorem]].
- We pick an integer $a$ such that $2\le a\le p-2$, and check if the equation holds or not. If it doesn't hold, we know that $p$ cannot be a prime number. Here, the base $a$ will be called a **Fermat Witness** for the compositeness of $p$.
- However, it is possible that the equation holds for a composite number. So if the equation holds, we don't have proof for primality. We only can say that $p$ is **probably** a prime. If it turns out that $p$ was composite, we call the base $a$ a **Fermat Liar**.
- The test will be repeated several times with random choices for $a$, if we find no Fermat Witness, it is very likely that the number is in fact prime.
- There exist some composite numbers $p$, where $a^{p-1}\equiv 1 \pmod p$ holds for all $a$ coprime to $p$. Such numbers are called **Carmichael Numbers**. The Fermat Primality test can identify these numbers only if we choose a base $a$ with $\gcd(a, p) \ne 1$.
- There are very few Carmichael numbers, only 646 exist below $10^9$.
```cpp
bool isPrimeFermat(int n, int iter=10)
{
	if (n < 4)
		return n == 2 || n == 3;
	for (int i = 0; i < iter; i++)
	{
		int a = 2 + rand() % (n - 3); // a in [2, n-2]
		if (binpow(a, n-1, n) != 1)
			return false;
	}
	return true;
}
```
Use [[Binary Exponentiation]] to efficiently compute  $a^{n-1}\bmod n$.
## Miller-Rabin Primality Test
Extends the ideas from Fermat Primality Test. Time complexity is $\le O((\ln n)^2)$.
For an odd number $n$, $n-1$ is even, so we factor out all powers of $2$:
$$n-1 = 2^s\cdot d$$
where $d$ is odd.
This allows us to factorize the equation of Fermat's little theorem:
$$
\large{
\begin{align}
a^{n-1}&\equiv 1 \bmod n \iff a^{2^sd} - 1\equiv 0 \bmod n\\
&\iff \left(a^{2^{s-1}d}+1\right)\left(a^{2^{s-1}d}-1\right)\equiv 0 \bmod n\\
&\iff \left(a^{2^{s-1}d}+1\right)\left(a^{2^{s-2}d}+1\right)\left(a^{2^{s-2}d}-1\right)\equiv 0 \bmod n\\
&\vdots\\
&\iff \left(a^{2^{s-1}d}+1\right)\left(a^{2^{s-2}d}+1\right)\cdots\left(a^{2^{0}d}+1\right)\left(a^{2^{0}d}-1\right)\equiv 0 \bmod n
\end{align}
}
$$
If $n$ is prime, then $n$ has to divide one of these factors.
For a base $2\le a\le n-2$ we check if either:
$$\Large a^d\equiv 1\mod n$$
holds or
$$\Large a^{2^rd}\equiv -1\mod n$$
holds for some $0\le r\le s-1$
- If we find a base $a$ which doesn't satisfy any of the above equalities, then we find a witness for the compositeness of $n$. Therefore, it will be proven that $n$ is not a prime number.
- It is however possible that the set of equations is satisfied for a composite number. In that case, the base $a$ is called a strong liar.
  If a base $a$ satisfies any one of the equations, $n$ is only strong probable prime.
  There are no numbers where all non-trivial bases lie. It is possible to show that at most $\frac{1}{4}$ of the bases can be strong liars.
- If $n$ is composite, we have a probability of $\ge 75\%$ that a random base will tell us that it is composite.
- By doing multiple iterations, choosing different random bases, we can tell with very high probability if the number is truly prime or if it is composite.
```cpp
bool checkComposite(int n, int a, int d, int s)
{
	int x = binpow(a, d, n);
	if (x == 1 || x == n - 1)
		return false;
	for (int i = 1; i < s; i++)
	{
		x = x * x % n;
		if (x == n - 1)
			return false;
	}
	return true;
}

bool isPrimeMillerRabin(int n, int iter=10)
{
	if (n < 4)
		return n == 2 || n == 3;
	int d = n - 1, s = 0; // n-1 = 2^s * d
	while ((d & 1) == 0)
	{
		d >>= 1;
		s++;
	}
	for (int i = 0; i < iter; i++)
	{
		int a = 2 + rand() % (n - 3);
		if (checkComposite(n, a, d, s))
			return false;
	}
	return true;
}
```
>[!tip] Before doing the Miller-Rabin test, we can test additionally if one of the first few prime numbers is a divisor. 88% of all numbers have a prime factor smaller than 100.
### Deterministic Version
Miller-Rabin showed that it is possible to make the algorithm deterministic by only checking all bases, time complexity $\le O((\ln n)^2)$.
Bach later showed that it is only necessary to test all bases $a \le 2(\ln n)^2$.
However, it turns out that for testing a 32-bit integer, it is only necessary to check the first 4 prime bases. And for testing a 64-bit integer, it is enough to check the first 12 prime bases.
```cpp
bool isPrimeDeterministic(int n)
{
	if (n < 2)
		return false;
	int d = n - 1, s = 0;
	while ((d & 1) == 0)
	{
		d >>= 1;
		s++;
	}
	for (int a : {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37})
	{
		if (n == a)
			return true;
		if (checkComposite(n, a, d, s))
			return false;
	}
	return true;
}
```