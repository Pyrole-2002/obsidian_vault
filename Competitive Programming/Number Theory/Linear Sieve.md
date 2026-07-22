- Linear Sieve deals with finding all prime numbers in a segment $[2; n]$.
- The standard way of solving this is to use the [[Sieve of Eratosthenes]], but that has runtime $O(n\log\log n)$.
- This algorithm also calculates factorizations of all numbers in the segment $[2; n]$ as a side effect that can be helpful.
- The weakness of linear sieve is using more memory. It makes sense to use this algorithm only for numbers up to order $10^7$.
#### Algorithm
Goal is to calculate Minimum Prime Factor $\text{lp}[i]$ for every $i$ in the segment $[2; n]$. Also store the list of all the found prime numbers in $\text{pr}[]$.
1. Initialize the values of $\text{lp}[i]$ with zeros, assuming all numbers are prime.
2. Iterate $i$ through numbers $2$ to $n$:
	- If $\text{lp}[i] = 0$, $i$ is prime, meaning we haven't found any smaller factors for it. Assign $\text{lp}[i] = i$ and append $i$ to the end of list $\text{pr}[]$.
	- If $\text{lp}[i] \ne 0$, $i$ is composite and its minimum prime factor is $\text{lp}[i]$.
3. Update values of $\text{lp}[]$ for the indexes divisible by $i$.
	1. Consider the numbers $x_j = i\cdot p_j$, where $p_j$ are all prime numbers less than or equal to $\text{lp}[i]$.
	2. Update values of $\text{lp}[x_j] = p_j$ for all above $x_j$.
```cpp
pair<vector<int>, vector<int>> linearSieve(int n)
{
	vector<int> lp(n + 1), pr;
	for (int i = 2; i <= n; i++)
	{
		if (lp[i] == 0)
		{
			lp[i] = i;
			pr.push_back(i);
		}
		for (int j = 0; i * pr[j] <= n; j++)
		{
			lp[i * pr[j]] = pr[j];
			if (pr[j] == lp[i])
				break;
		}
	}
	return {lp, pr};
}
```