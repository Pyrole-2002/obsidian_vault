## Trial Division
- Divide by each possible divisor $d$.
- Test divisors $2\le d \le \sqrt n$, with a time complexity $O(\sqrt n)$.
- The smallest divisor must be prime, we remove the factored number and continue the process.
- If no divisor found in the range $[2; \sqrt n]$, then the number has to be prime.
```cpp
vector<long long> factorTrialDivision(long long n)
{
	vector<long long> factorization;
	for (long long d = 2; d*d <= n; d++)
	{
		while (n % d == 0)
		{
			factorization.emplace_back(d);
			n /= d;
		}
	}
	if (n > 1) // prime numbers
		factorization.emplace_back(n);
	return factorization;
}
```
### Wheel Factorization
- Optimization of the trial division.
- One we know that the number is not divisible by 2, we don't check other even numbers. After factoring out 2 and getting an odd number, we simply start with 3 and only consider other odd divisors.
- When extended further, if the number is not divisible by 3, we can also ignore all other multiples of 3.
- Simply, we can also ignore all multiples of 5 once the number isn't divisible by 5.
- Effectively, we ignore all multiples of 2, 3, 5. It can be shown that the remaining numbers form a pattern represented in the below code.
- It is best to store the skipping strides.
- If we add more primes, better optimizations will be reached but the skipping stride array will become larger.
```cpp
vector<long long> factorWheel(long long n)
{
	vector<long long> factorization;
	for (int d : {2, 3, 5})
	{
		while (n % d == 0)
		{
			factorization.emplace_back(d);
			n /= d;
		}
	}
	
	static array<int, 8> increments = {4, 2, 4, 2, 4, 6, 2, 6};
	int i = 0;
	for (long long d = 7; d*d <= n; d += increments[i++])
	{
		while (n % d == 0)
		{
			factorization.emplace_back(d);
			n /= d;
		}
		if (i == 8) // loop through increments
			i = 0;
	}
	
	if (n > 1)
		factorization.emplace_back(n);
	return factorization;
}
```
#### Precomputed Primes
- Extension of the wheel factorization.
- We only test for prime numbers as divisors.
- We precompute all prime numbers using [[Sieve of Eratosthenes]] until $\sqrt n$ and test them individually.
```cpp
vector<long long> primes;

vector<long long> factorPrecomputed(long long n)
{
	vector<long long> factorization;
	for (long long d : primes)
	{
		if (d*d > n)
			break
		while (n % d == 0)
		{
			factorization.emplace_back(d);
			n /= d;
		}
	}
	if (n > 1)
		factorization.emplace_back(n);
	return factorization
}
```
## Fermat's Factorization Method
- We can write an odd composite number $n = p\cdot q$ as the difference of two squares $n = a^2 - b^2 = (a - b)(a + b)$.
$$n = \left(\frac{p+q}{2}\right)^2 - \left(\frac{p-q}{2}\right)^2$$
- Fermat's factorization method tries to exploit this fact by guessing the first square $a^2$, and checking if the remaining part, $b^2 = a^2 - n$, is also a square number. If it is, then we have found the factors $a-b$ and $a+b$ of $n$.
```cpp
int factorFermat(int n)
{
	int a = ceil(sqrt(n));
	int b2 = a*a - n; // b^2 = a^2 - n
	int b = round(sqrt(b2));
	while (b*b != b2)
	{
		a++;
		b2 = a*a - n;
		b = round(sqrt(b2));
	}
	return a - b;
}
```
- This method can be very fast if the deference between the factors $p$ and $q$ is small. The algorithm runs in time $O(|p-q|)$ complexity.
- In practice, this method isn't used because once factors become further apart, it is extremely slow.
---
https://cp-algorithms.com/algebra/factorization.html