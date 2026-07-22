Sieve of Eratosthenes is an algorithm for finding all the prime numbers in a segment $[1;n]$ using $O(n\log\log n)$ time complexity.
#### Steps:
1. At the beginning, we write down all numbers between $2$ and $n$.
2. Mark $2$ as prime. Mark all proper multiples of $2$ as composite.
3. Find the next number that hasn't been marked composite and mark it as prime. Then mark all proper multiples of this new prime number as composite.
4. Repeat the step $3$ until we have processed all numbers in the row.
```cpp
vector<bool> sieveOfEratosthenes(int n)
{
	vector<bool> is_prime(n + 1, true);
	is_prime[0] = is_prime[1] = false; // 0 and 1 are not prime numbers
	for (int i = 2; (long long)i*i <= n; i++)
	{
		if (is_prime[i])
		{
			for (int j = i*i; j <= n; j += i)
			{
				is_prime[j] = false;
			}
		}
	}
	return is_prime;
}
```
- `long long` is used to ensure $i^2$ doesn't overflow the size of `int`.
- Start sifting from $i^2$ till $n$, because the numbers below $i^2$ would have been already marked by smaller primes.
### Find primes in a range
- Lets say we need to find all prime numbers in a range $[L, R]$ of small size (range of the order of $10^7$), where $L, R$ may be very large (order of $10^{12}$).
- To solve this, we use the idea of the Segmented Sieve.
- We pre-generate all primes up to $\sqrt R$, and use those to mark all composite numbers in the segment $[L, R]$.
```cpp
vector<bool> segmentedSieve(long long L, long long R)
{
    // generate all primes up to sqrt(R)
    long long lim = sqrt(R);
    vector<bool> mark(lim + 1, false);
    vector<long long> primes;
    for (long long i = 2; i <= lim; i++)
    {
        if (!mark[i])
        {
            primes.emplace_back(i);
            for (long long j = i * i; j <= lim; j += i)
                mark[j] = true;
        }
    }

    // create sieve for segment
    vector<bool> is_prime(R - L + 1, true);
    for (long long prime : primes)
        for (long long j = max(prime*prime, ((L+prime-1)/prime) * prime);
        j <= R; j += prime)
        {
            is_prime[j - L] = false;
        }
    if (L == 1)
        is_prime[0] = false;
    return is_prime;
}
```
