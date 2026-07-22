## Enumerating All Submasks of a Given Mask
Given a bitmask $m$, you want to efficiently iterate through all of its submasks, that is, masks $s$ in which only bits that were included in mask $m$ are set. A submask $s$ is any mask that can be formed by clearing some (or none) of the set bits of $m$ to $0$.
We can iterate through the submasks using below implementations:
```cpp
for (int s = m; s; s = (s-1)&m)
{
	// code here
}
```
The above loop iterates all submasks of $m$ without repetition and in descending order.
Suppose we have a current bitmask $s$, and we want to move on to the next bitmask, by subtracting $1$ from the mask $s$, we clear the rightmost set bit and set all bits to the right of it. This gives us the largest number less than $s$. Then we remove all the "extra" $1$ bits that are not included in $m$ and therefore shouldn't be part of submask by taking bitwise AND to get the next largest bitmask in descending order.
### Iterating Through All Masks with their Submasks
In many problems, we iterate through all bitmasks and for each mask, iterate through all of its submasks:
```cpp
for (int m = 0; m < (1<<n); m++)
{
	for (int s = m; s; s = (s-1)&m)
	{
		// code here
	}
}
```
The inner loop executes a total of $O(3^n)$ iterations.
##### Proof:
Note that if mask $m$ has $k$ set bits, then it will have $2^k$ submasks. As we have a total of $\binom{n}{k}$ masks with $k$ set bits, the total number of combinations for for all $s$ and $m$ will be:
$$\sum\limits_{k=0}^{n}\binom{n}{k}\cdot2^k = (1+2)^n = 3^n$$