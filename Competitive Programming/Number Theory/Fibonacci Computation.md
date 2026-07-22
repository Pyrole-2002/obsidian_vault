The Fibonacci Sequence is defined as follows: $F_0 = 0, F_1 = 1, F_n = F_{n-1} + F_{n-2}$.
Fibonacci numbers have the following properties:
- **Cassini's Identity:** $F_{n-1} \cdot F_{n+1} - F_n^2 = (-1)^n$
- **Addition Rule:** $F_{n+k} = F_k \cdot F_{n+1} + F_{k-1} \cdot F_n$
- When $k=n$, $F_{2n} = F_n(F_{n+1} + F_{n-1})$. By induction, for any positive integer $k$, $F_{nk}$ is multiple of $F_n$. The inverse is also true: if $F_m$ is multiple of $F_n$, then $m$ is a multiple of $n$.
- **GCD Identity:** $\text{GCD}(F_m, F_n) = F_{\text{GCD}(m, n)}$
- Fibonacci numbers are the worst possible inputs for [[Euclidean Algorithm]] according to Lame's Theorem.
- **Binet's Formula:** $\Large{F_n = \frac{\left(\frac{1+\sqrt 5}{2}\right)^n - \left(\frac{1-\sqrt 5}{2}\right)^n}{\sqrt 5}}$, for $n > 1$, since the $2^{\text{nd}}$ term approaches $0$ exponentially: $\Large{F_n = \frac{\left(\frac{1+\sqrt 5}{2}\right)^n}{\sqrt 5}}$
### Matrix Form of Fibonacci Numbers
$$
\begin{pmatrix}
F_{n+1} & F_n\\
F_n & F_{n-1}
\end{pmatrix}
=
\begin{pmatrix}
1 & 1\\
1 & 0
\end{pmatrix} ^ n
$$
Using [[Binary Exponentiation]] in above, we can calculate $F_n$ in $O(\log n)$ time.
#### Fast Doubling Method
By expanding the above formula for $n=2k$:
$$
\begin{pmatrix}
F_{2k+1} & F_2k\\
F_2k & F_{2k-1}
\end{pmatrix}
=
\begin{pmatrix}
1 & 1\\
1 & 0
\end{pmatrix} ^ {2k}
=
\begin{pmatrix}
F_{k+1} & F_k\\
F_k & F_{k-1}
\end{pmatrix} ^ {2}
$$
we can find the following simpler equations:
$$
\begin{align}
&F_{2k+1} = F_{k+1}^2 + F_k^2\\
&F_{2k} = F_{k}(2F_{k+1} - F_k)
\end{align}
$$
Using the above two equation, Fibonacci numbers can be calculated easily:
```cpp
pair<int, int> fib(int n) 
{
	if (n == 0)
		return {0, 1};
	auto p = fib(n >> 1);
	int a = p.first;
	int b = p.second;
	
	int c = a * (2*b - a);
	int d = b*b + a*a;
	if (n & 1)
		return {d, c+d};
	else
		return {c, d};
}
```
The above code returns $F_n$ and $F_{n+1}$ as a pair.
### Fibonacci Coding
According to Zeckendorf's theorem, any natural number $n$ can be uniquely represented as a sum of Fibonacci Numbers:
$$N = F_{k_1} + F_{k_2} + \dots + F_{k_r}$$
such that $k_n \ge k_{n+1}+2, k_r \ge 2$, meaning that the representation cannot use two consecutive Fibonacci Numbers.
Any number can be uniquely encoded in the Fibonacci Encoding, we describe this representation with binary codes $d_0d_1d_2\dots d_s1$, where $d_i$ is $1$ if $F_{i+2}$ is used in the representation. The code is appended by a $1$ to indicate the end of the code word. This is the only occurrence where two consecutive $1$-bits appear.
$$
\begin{eqnarray}
1    &=& 1       &=& F_2         &=& (11)_F\\
2    &=& 2       &=& F_3         &=& (011)_F\\
3    &=& 3       &=& F_4         &=& (0011)_F\\
4    &=& 3+1     &=& F_4+F_2     &=& (1011)_F\\
5    &=& 5       &=& F_5         &=& (00011)_F\\
6    &=& 5+1     &=& F_5+F_2     &=& (10011)_F\\
7    &=& 5+2     &=& F_5+F_3     &=& (01011)_F\\
8    &=& 8       &=& F_6         &=& (000011)_F\\
19   &=& 13+5+1  &=& F_7+F_5+F_2 &=& (1001011)_F
\end{eqnarray}
$$
The encoding of any integer $n$ can be done by the following greedy algorithm:
1. Iterate through the Fibonacci numbers in descending order to find the largest Fibonacci number less than or equal to $n$. Suppose this is $F_i$.
2. Subtract $F_i$ from $n$ and put a $1$ in the $i-2$ position from left (0-indexed).
3. Repeat until there is no remainder.
4. Add a $1$ to the rightmost end.
## Periodicity
The Fibonacci sequence modulo $p$ is periodic.
The periodic sequence always starts with $F_0,F_1 \equiv 0,1$.
### Pisano Period
The pisano period refers to the periodic sequence of Fibonacci numbers taken modulo a positive integer $p$. The length of the repeating sequence of Fibonacci numbers modulo $p$ is called the pisano period of $p$.
###### Definition
The Pisano Period $\pi(p)$ is the length of the cycle in the sequence of Fibonacci numbers modulo $p$.
If the Fibonacci sequence modulo $p$ is given by:
$$F(n) \mod p$$
then the Pisano Period $\pi(p)$ is the smallest positive integer $k$ such that:
$$F(n+k)\equiv F(n)\pmod p$$
for all integers $n$.