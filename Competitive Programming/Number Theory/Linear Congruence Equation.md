The equation is of the form:
$$ax\equiv b\pmod n$$
where $a$, $b$ and $n$ are given integers and $x$ is an unknown integer.
We need to find the value of $x$ from the interval $[0; n-1]$.
### Solution by finding [[Modular Inverse]]
Consider a simpler case where $a$ and $n$ are coprime. Then we can find the modular inverse of $a$ and get a unique solution:
$$x\equiv b\cdot a^{-1}\pmod n$$
Consider the case where $a$ and $n$ are not coprime. Then the solution will not always exist. Let $g=\gcd(a, n)$ which would be greater than one.
Then, if $b$ is not divisible by $g$, there is no solution. If $g$ divides $b$, then by dividing both sides of the equation by $g$, we receive:
$$a'x\equiv b'\pmod{n'}$$
in which $a'$ and $n'$ are already coprime. We get $x'$ as solution for $x$. It is clear that $x'$ will also be a solution of the original equation. However, it will not be the only solution. The original equation will have exactly $g$ solutions:
$$x_i = (x'+in')\pmod{n}\quad \text{for }i=0,1\dots g-1$$
Therefore, the number of solutions of the linear congruence equation is equal to either $g$ or to $0$.
### Solution using [[Euclidean Algorithm#Extended Euclidean Algorithm|Extended Euclidean Algorithm]]
We can rewrite the equation to the following Diophantine Equation:
$$ax+nk = b$$
where $x$ and $k$ are unknown integers.
The method of solving this is described in the article of [[Linear Diophantine Equation]].
This method is equivalent to the Modular Inverse method.