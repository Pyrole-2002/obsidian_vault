- Simplified Description: Quantum states are represented by vectors; operations are represented by unitary matrices.
- General Description: Quantum states are represented by density matrices; allows for a more general class of measurements and operations.
## Classical Information
Consider a physical system that stores information: let us call it $X$.
Assume $X$ can be in one of a finite number of *classical states* at each moment.
Denote this classical state set by $\Sigma$.
There may be uncertainty about the classical state of a system, where each classical state has some probability associated with it.

> **Classical State:** A configuration of the system that can be recognized and described unambiguously without any uncertainty or error.

For example: If $X$ is a bit, then perhaps it is in the classical state 0 with probability 3/4 and in the classical state 1 with probability 1/4. This is a probabilistic state of $X$.
$$
\Pr(X = 0) = \frac{3}{4} \quad \text{and} \quad \Pr(X = 1) = \frac{1}{4}
$$
A way to represent this probabilistic state is by a column vector:
$$
\begin{pmatrix}
\dfrac{3}{4} \\[1em]
\dfrac{1}{4}
\end{pmatrix}
\quad
\begin{matrix}
\longleftarrow \text{ entry corresponding to 0} \vphantom{\dfrac{3}{4}} \\[1em]
\longleftarrow \text{ entry corresponding to 1} \vphantom{\dfrac{1}{4}}
\end{matrix}
$$
This vector is a probability vector:
- All entries are non-negative real numbers.
- The sum of the entries is 1.
### Dirac Notation
Let $\Sigma$ be any classical state set, and assume the elements of $\Sigma$ have been placed in correspondence with the integers $1,\dots,|\Sigma|$.
We denote by $|a\rangle$ the column vector having a 1 in the entry corresponding to $a \in \Sigma$, with 0 for all other entries.
$$
\begin{pmatrix}
\dfrac{3}{4} \\[0.8em]
\dfrac{1}{4}
\end{pmatrix}
= \frac{3}{4} |0\rangle + \frac{1}{4} |1\rangle
$$
We denote by $\langle a |$ the row vector having a 1 in the entry corresponding to $a \in \Sigma$.
For example, if $\Sigma = \{0,1\}$, then:
$$
\langle 0 | = \begin{pmatrix} 1 & 0 \end{pmatrix} \quad \text{and} \quad \langle 1 | = \begin{pmatrix} 0 & 1 \end{pmatrix}
$$
Multiplying a row vector to a column vector yields a scalar:
$$
\langle a | b \rangle = \langle a | | b \rangle = \begin{cases} 
1 & a = b \\ 
0 & a \neq b 
\end{cases}
$$
Multiplying a column vector to a row vector yields a matrix. In general, the matrix $|a\rangle\langle b|$ has a 1 in the $(a,b)$-entry and 0 for all other entries.

The Dirac notation can be used for arbitrary vectors; any name can be used in place of a classical state. Kets are column vectors, Bras are row vectors.
The notation $|\psi\rangle$ is commonly used to refer to an arbitrary vector:
$$
|\psi\rangle = \frac{1 + 2i}{3} |0\rangle - \frac{2}{3} |1\rangle
$$
$$
\langle\psi| = \frac{1 - 2i}{3} \langle0| - \frac{2}{3} \langle1|
$$
For any column vector $|\psi\rangle$, the row vector $\langle\psi|$ is the conjugate transpose of $|\psi\rangle$:

$$
\langle\psi| = |\psi\rangle^\dagger
$$
### Measuring Probabilistic States
When we measure a system $X$ while it is in some probabilistic state, we see a classical state, chosen at random according to the probabilities.
Suppose we see the classical state $a \in \Sigma$.
This changes the probabilistic state of $X$ (from our viewpoint): having recognized that $X$ is in the classical state $a$, we now have $\Pr(X = a) = 1$.
This probabilistic state is represented by the vector $|a\rangle$.
### Deterministic Operations
Every function $f: \Sigma \rightarrow \Sigma$ describes a deterministic operation that transforms the classical state $a$ into $f(a) \forall a \in \Sigma$.
Given any function $f : \Sigma \rightarrow \Sigma$, there is a unique matrix $M$ satisfying $M|a\rangle = |f(a)\rangle \forall a \in \Sigma$.
This matrix has exactly one 1 in each column, and 0 for all other entries:
$$
M(b, a) = \begin{cases} 
1 & b = f(a) \\ 
0 & b \neq f(a) 
\end{cases}
$$
$b$ is row and $a$ is column.
The action of this operation is described by matrix-vector multiplication:
$$
v \longmapsto Mv
$$
For $\Sigma = \{0, 1\}$, there are four functions of the form $f : \Sigma \to \Sigma$:

$$
\begin{array}{c|c}
a & f_1(a) \\ \hline
0 & 0 \\
1 & 0
\end{array}
\qquad
\begin{array}{c|c}
a & f_2(a) \\ \hline
0 & 0 \\
1 & 1
\end{array}
\qquad
\begin{array}{c|c}
a & f_3(a) \\ \hline
0 & 1 \\
1 & 0
\end{array}
\qquad
\begin{array}{c|c}
a & f_4(a) \\ \hline
0 & 1 \\
1 & 1
\end{array}
$$

Here are the matrices corresponding to these functions:

$$
M_1 = \begin{pmatrix} 1 & 1 \\ 0 & 0 \end{pmatrix} \qquad
M_2 = \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix} \qquad
M_3 = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix} \qquad
M_4 = \begin{pmatrix} 0 & 0 \\ 1 & 1 \end{pmatrix}
$$
$M$ may also be expressed as:
$$
M = \sum_{b \in \Sigma} |f(b)\rangle \langle b|
$$
### Probabilistic Operations
Probabilistic operations are classical operations that may introduce randomness or uncertainty.
Probabilistic operations are described by stochastic matrices:
- All entries are non-negative real numbers.
- The entries in every column sum to 1.
For example, here is a probabilistic operation on a bit:
	If the classical state is 0, then do nothing.
	If the classical state is 1, then flip the bit with probability 1/2.
$$
\begin{pmatrix}
1 & \dfrac{1}{2} \\[0.8em]
0 & \dfrac{1}{2}
\end{pmatrix}
$$
### Composing Operations
Suppose $X$ is a system and $M_1,\dots,M_n$ are stochastic matrices representing probabilistic operations on $X$.
Applying the first probabilistic operation to the probability vector $v$, then applying the second probabilistic operation to the result yields: $M_2(M_1v) = (M_2M_1)v$.
The probabilistic operation obtained by composing the first and second probabilistic operations is represented by the matrix product $M_2M_1$.
Composing the probabilistic operations represented by the matrices $M_1,\dots,M_n$ (in that order) is represented by the matrix product $M_n\dots M_1$.
The order is important, matrix multiplication is not commutative.
## Quantum Information
A quantum state of a system is represented by a column vector whose indices are placed in correspondence with the classical states of that system:
- The entries are complex numbers.
- The sum of the absolute values squared of the entries must equal 1.
The Euclidean norm for vectors with complex number entries is defined like this:
$$
v = \begin{pmatrix} \alpha_1 \\ \vdots \\ \alpha_n \end{pmatrix} \implies \|v\| = \sqrt{\sum_{k=1}^n |\alpha_k|^2}
$$
Quantum state vectors are therefore unit vectors with respect to this norm.
#### Examples of Qubit States
- Standard basis states: $|0\rangle$ and $|1\rangle$
- Plus/Minus states:
$$
|+\rangle = \frac{1}{\sqrt{2}} |0\rangle + \frac{1}{\sqrt{2}} |1\rangle \quad \text{and} \quad |-\rangle = \frac{1}{\sqrt{2}} |0\rangle - \frac{1}{\sqrt{2}} |1\rangle
$$
