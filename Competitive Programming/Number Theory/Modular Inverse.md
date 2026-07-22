- A modular multiplicative inverse of an integer $a$ is an integer $x$ such that $a\cdot x$ is congruent to $1$ modular some modulus $m$.
$$a\cdot x\equiv 1\mod m$$
- We can denote $x$ simply with $a^{-1}$.
- Modular inverse does not always exist. Modular inverse exists if and only if $a$ and $m$ are coprime.
### Modular Inverse using [[Euclidean Algorithm#Extended Euclidean Algorithm|Extended Euclidean Algorithm]]
Consider the following equation with unknown $x$ and $y$:
$$ax+my=1$$
This is a [[Linear Diophantine Equation]] in two variables.
Since $a$ and $m$ are coprime, $\gcd(a, m) = 1$ fits the equation in [[Euclidean Algorithm#Extended Euclidean Algorithm|Extended Euclidean Algorithm]].
Taking modulo $m$ on both sides, we get rid of the term $my$:
$$ax\equiv 1\mod m$$
Therefore, the modular inverse of $a$ is $x$.
```cpp
int modInverseExtendedEuclidean(int a, int m)
{
	int x, y;
	int g = gcdExtendedEuclidean(a, m, x, y);
	if (g != 1) // No Solution
		return 0;
	return (x % m + m) % m;
}
```
### Modular Inverse using [[Binary Exponentiation]]
This method uses [[Euler's Totient Function#Euler's Theorem|Euler's Theorem]] which states that:
$$a^{\phi(m)}\equiv 1\mod m$$
if $a$ and $m$ are coprime.
If $m$ is a prime number, this simplifies to [[Fermat's Little Theorem]]:
$$a^{m-1}\equiv 1\mod m$$
Multiply both sides of Euler's Theorem equivalence by $a^{-1}$:
- For an arbitrary (but coprime) modulus $m$: $a^{\phi(m)-1}\equiv a^{-1}\mod m$
- For a prime modulus $m$: $a^{m-2}\equiv a^{-1}\mod m$
From the above results, we can easily find modular inverse using binary exponentiation which works in $O(\log m)$.
However, in the case when $m$ is not a prime number, we need to calculate Euler Phi Function, which involves factorization of $m$ (hard). Only if the prime factorization of $m$ is known, then the complexity of this method remains $O(\log m)$.
### Modular Inverse using Euclidean Division
Given a prime modulus $m > a$ (or apply modulo to make it smaller in 1 step), according to Euclidean Division:
$$m=ka+r$$
where $k = \left\lfloor\frac{m}{a}\right\rfloor$ and $r=m\bmod a$, then
$$
\begin{align}
& \implies & 0          & \equiv k \cdot a + r   & \mod m \\
& \iff & r              & \equiv -k \cdot a      & \mod m \\
& \iff & r \cdot a^{-1} & \equiv -k              & \mod m \\
& \iff & a^{-1}         & \equiv -k \cdot r^{-1} & \mod m
\end{align}
$$
Note that this does not hold if $m$ is not prime, since the existence of $a^{-1}$ does not imply the existence of $r^{-1}$ in general.
```cpp
int modInversePrime(int a, int m) // if m is prime
{
	return a <= 1 ? a : m - (long long)(m/a) * modInverse(m % a) % m;
}
```
The time complexity of this recursion is not known but it is extremely fast.
Applying this, we can also precompute the modular inverse for every number in the range $[1; m-1]$ in $O(m)$
```cpp
vector<int> modInverseRangePrime(int a, int m) // if m is prime
{
	vector<int> inv(n);
	inv[1] = 1;
	for (int i = 2; i < m; i++)
		inv[i] = m - (long long)(m/i) * inv[m % i] % m;
	return inv;
}
```
### Modular Inverse for Array of Numbers
Suppose we have an array of invertible numbers $x_1, x_2\dots x_n$. Instead of computing the inverse for every number, we can expand the fraction by the prefix product (excluding itself) and suffix product (excluding itself), and end up only computing a single inverse instead.
$$
\begin{align}
x_i^{-1} = \frac{1}{x_i} &= \frac{\prod\limits_{1}^{i-1}x_j\cdot 1\cdot\prod\limits_{i+1}^{n}x_j}{\prod\limits_{1}^{i-1}x_j\cdot x_i\cdot\prod\limits_{i+1}^{n}x_j}\\
&= \frac{\text{prefix}_{i-1}\cdot\text{suffix}_{i+1}}{\prod\limits_1^nx_j}\\
&= \text{prefix}_{i-1}\cdot\text{suffix}_{i+1}\cdot\left(\prod\limits_1^nx_j\right)^{-1}
\end{align}
$$
Therefore, we can just compute the modular inverse for product of all numbers and then multiply it by the prefix product and suffix product excluding the number itself.
```cpp
vector<int> modInverseArray(vector<int> a, int m)
{
	vector<int> inv(a.size());
	if (a.size() == 0)
		return inv;
	long long prod = 1;
	for (int i = 0; i < a.size(); i++)
	{
		inv[i] = prod;
		prod = (prod * a[i]) % m;
	}
	int x, y;
	int g = gcdExtendedEuclidean(prod, m, x, y);
	if (g != 1) // No Solution
	{
		for (int i = 0; i < a.size(); i++)
			inv[i] = -1;
		return inv;
	}
	x = (x % m + m) % m;
	for (int i = a.size() - 1; i >= 0; i--)
	{
		inv[i] = (inv[i] * x) % m;
		x = (x * a[i]) % m;
	}
	return inv;
}
```