Given two non-negative integers $a$ and $b$, Euclidean Algorithm helps find the GCD for both.
$$
\gcd(a, b) =
\begin{cases}
a, & \text{if } b = 0\\
\gcd(b, a\bmod{b}), & \text{otherwise}
\end{cases}
$$
#### Implementation
```cpp
// recursive (slower)
int gcdRecursive1(int a, int b)
{
	if (b == 0)
	{
		return a;
	}
	else
	{
		return gcd(b, a % b);
	}
}

int gcdRecursive2(int a, int b)
{
	return b ? gcd(b, a % b) : a;
}

// iterative (faster)
int gcdIterative(int a, int b)
{
	while (b)
	{
		a %= b;
		swap(a, b);
	}
	return a;
}
```

> [!tip] Always use C++ inbuilt [[Tricks#Inbuilt GCD|GCD]] function.
## Time Complexity
- The time complexity of Euclidean Algorithm is estimated by Lame's Theorem, which establishes a connection between the Euclidean Algorithm and the [[Fibonacci Computation]].
- Consecutive Fibonacci Numbers are the worst case input for Euclidean Algorithm.
- Given that the Fibonacci Numbers grow exponentially, the Euclidean Algorithm works in $O\Big(\log\big(\min(a, b)\big)\Big)$.
### LCM
$$
\DeclareMathOperator{\lcm}{lcm}
a\cdot b = \lcm(a,b)\cdot\gcd(a,b)
$$
```cpp
int lcm(int a, int b)
{
	return (a / gcd(a, b)) * b; // avoids integer overflow
}
```
# Binary GCD
- The slow part of the algorithm is the modulo operations. These are a lot slower than the simple addition, subtraction or bitwise operations. So we use some properties to implement a faster binary GCD function (same as the inbuilt GCD function).
- If both numbers are even, then we can factor out a $2$ from both and compute the GCD of the remaining.
  $\gcd(2a, 2b) = 2\gcd(a, b)$
- If one of the numbers is even and other is odd, then we can remove the factor of $2$ from the even one.
  $\gcd(2a, b) = \gcd(a, b)$
- If both numbers are odd, then we can subtract one number from the other and calculate GCD.
  $gcd(a, b) = \gcd(a-b, b)$
```cpp
int gcdBinary(int a, int b)
{
	if (!a || !b)
	{
		return a | b; // if either one of a or b is 0, return the non-zero value
	}
	unsigned shift = __builtin_ctz(a | b); // calculates the number of trailing zeroes in binary form of a | b
	a >>= __builtin_ctz(a); // remove trailing zeroes from 
	do
	{
		b >>= __builtin_ctz(b);
		if (a > b)
		{
			swap(a, b);
		}
		b -= a;
	}
	while (b);
	return a << shift;
}
```
# Extended Euclidean Algorithm
The extended euclidean algorithm also represents the GCD in terms of coefficients of $a$ and $b$ which are $x$ and $y$ respectively.
$$a\cdot x + b\cdot y = \gcd(a, b)$$
- Denoting the GCD of $a$ and $b$ with $g$.
- The euclidean algorithm ends with $b=0$ and $a=g$. For these parameters, $(x, y) = (1, 0)$.
- We now need to figure out how the coefficients change during the transition of parameters from $(a, b)$ to $(b, a\bmod b)$.
- We can represent $a\bmod b$ as:
$$
a\bmod b = a - \left\lfloor\frac{a}{b}\right\rfloor \cdot b
$$
- Assuming coefficients $(x, y)$ for $(a, b)$ , and $(x', y')$ for $(b, a\bmod b)$:
$$
\begin{align}
&a\cdot x+b\cdot y &= g\tag{1}\\
&b\cdot x'+(a\bmod b)\cdot y' &= g\\
\implies\quad &b\cdot x'+\left(a - \left\lfloor\frac{a}{b}\right\rfloor \cdot b\right)\cdot y' &= g\\
\implies\quad &a\cdot y'+b\cdot\left(x' - y'\cdot\left\lfloor\frac{a}{b}\right\rfloor\right) &= g\tag{2}\\
\end{align}
$$
- Comparing equations $(1)$ and $(2)$:
$$
\begin{align}
x &= y'\\
y &= x'-y'\cdot\left\lfloor\frac{a}{b}\right\rfloor
\end{align}
$$
### Implementation
```cpp
// Recursive
int gcdExtendedRecursive(int a, int b, int& x, int& y)
{
	if (b == 0)
	{
		x = 1;
		y = 0;
		return a;
	}
	int x1, y1;
	int d = gcdExtendedRecursive(b, a % b, x1, y1);
	x = y1;
	y = x1 - y1*(a/b);
	return d;
}

// Iterative
int gcdExtendedIterative(int a, int b, int& x, int& y)
{
	x = 1, y = 0;
	int x1 = 0, y1 = 1, a1 = a, b1 = b;
	while (b1)
	{
		int q = a1 / b1;
		tie(x, x1) = make_tuple(x1, x - q*x1);
		tie(y, y1) = make_tuple(y1, y - q*y1);
		tie(a1, b1) = make_tuple(b1, a1 - q*b1);
	}
	return a1;
}
```