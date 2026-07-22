> [!tip] For any integer $n > 1$, there cannot be more than one prime divisor of $n$ that is greater than $\sqrt n$.
## Number of Divisors
If the prime factorization of $n = p_1^{e_1}\cdot p_2^{e_2}\dots p_k^{e_k}$, where $p_i$ are distinct prime numbers, then the number of divisors is:
$$d(n) = (e_1+1)(e_2+1)\dots(e_k+1)$$
$d(n)$ is a multiplicative function:
$$d(ab) = d(a)\cdot d(b)$$
```cpp
long long numDivisors(long long num)
{
	long long total = 1;
	for (int i = 2; (long long)i*i <= num; i++)
	{
		if (num % i == 0)
		{
			int e = 0;
			do
			{
				num /= i;
				e++;
			} while (num % i == 0);
			total *= e + 1;
		}
	}
	if (num > 1) // if a prime > sqrt(n) remains
		total *= 2;
	return total;
}
```
## Sum of Divisors
If the prime factorization of $n = p_1^{e_1}\cdot p_2^{e_2}\dots p_k^{e_k}$, where $p_i$ are distinct prime numbers, then the sum of divisors is:
$$\sigma(n) = \frac{p_1^{e_1+1}-1}{p_1-1}\cdot\frac{p_2^{e_2+1}-1}{p_2-1}\dots\frac{p_k^{e_k+1}-1}{p_k-1}\cdot$$
$\sigma(n)$ is a multiplicative function:
$$\sigma(ab) = \sigma(a)\cdot \sigma(b)$$
```cpp
long long sumDivisors(long long num)
{
	long long total = 1;
	for (int i = 2; (long long)i*i <= num; i++)
	{
		if (num % i == 0)
		{
			int e = 0;
			do
			{
				num /= i;
				e++;
			} while (num % i == 0);
			long long sum = 0, pow = 1;
			do
			{
				sum += pow;
				pow *= i;
			} while (e-- > 0);
			total *= sum;
		}
	}
	if (num > 1)
		total *= num + 1;
	return total;
}
```