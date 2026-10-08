## Classical States
- Suppose that we have two systems:
	- $X$ is a system having classical state set $\Sigma$.
	- $Y$ is a system having classical state set $\Gamma$.
- Imagine that $X$ and $Y$ are placed side-by-side, with $X$ on the left and $Y$ on the right, and viewed together as if they form a single system.
- We denote this new compound system by $(X,Y)$ or $XY$.
- The classical state set of $(X,Y)$ is the cartesian product $\Sigma\times\Gamma=\{(a,b):a\in\Sigma \text{ and } b\in\Gamma\}$.

- Suppose $X_1,\dots,X_n$ are systems having classical state sets $\Sigma_1,\dots,\Sigma_n$ respectively.
- The classical state set of the n-tuple $(X_1,\dots,X_n)$, viewed as a single compound system, is the cartesian product $\Sigma_1\times\cdots\times\Sigma_n=\{(a_1,\dots,a_n):a_1\in\Sigma_1,\dots,a_n\in\Sigma_n\}$.
- An n-tuple $(a_1,\dots,a_n)$ may also be written as a string $a_1\cdots a_n$.
- Cartesian products of classical state sets are ordered lexicographically:
	- We assume the individual classical state sets are already ordered.
	- Significance decreases from left to right.
## Probabilistic States
- Probabilistic states of compound systems associate probabilities with the cartesian product of the classical state sets of the individual systems.
- For example below is a probabilistic state of a pair of bits $(X,Y)$:
$$
\text{Pr}((X,Y) = (0,0))=\frac{1}{2}\\
\text{Pr}((X,Y) = (0,1))=0\\
\text{Pr}((X,Y) = (1,0))=0\\
\text{Pr}((X,Y) = (0,0))=\frac{1}{2}\\

\begin{pmatrix} \frac{1}{2} \\ 0 \\ 0 \\ \frac{1}{2} \end{pmatrix} \begin{array}{l} \leftarrow \text{probability associated with state } 00 \vphantom{\frac{1}{2}} \\ \leftarrow \text{probability associated with state } 01 \\ \leftarrow \text{probability associated with state } 10 \\ \leftarrow \text{probability associated with state } 11 \vphantom{\frac{1}{2}} \end{array}
$$
- For a given probabilistic state of $(X,Y)$, we say that $X$ and $Y$ are independent if:
$$
\text{Pr}((X,Y)=(a,b)) = \text{Pr}(X=a)\text{Pr}(Y=b) \quad\forall\quad a\in \Sigma \text{ and } b\in\Gamma
$$
- Suppose that a probabilistic state of $(X,Y)$ is expressed as a vector:
$$
|\pi\rangle = \sum_{(a,b) \in \Sigma \times \Gamma} p_{ab} |ab\rangle
$$
	The systems $X$ and $Y$ are independent if there exist probability vectors:
$$
|\phi\rangle = \sum_{a \in \Sigma} q_a |a\rangle\quad\text{ and }\quad|\psi\rangle = \sum_{b \in \Gamma} q_b |b\rangle
$$
	Such that $p_{ab}=q_ar_b\quad\forall\quad a\in\Sigma\text{ and }b\in\Gamma$.
### Tensor Products of Vectors
- The tensor product of two vectors $|\phi\rangle = \sum_{a \in \Sigma} \alpha_a |a\rangle\text{ and }|\psi\rangle = \sum_{b \in \Gamma} \beta_b |b\rangle$ is the vector:
$$
|\phi\rangle \otimes |\psi\rangle = \sum_{(a, b) \in \Sigma \times \Gamma} \alpha_a \beta_b |ab\rangle
$$
	Equivalently, the vector $|\pi\rangle=|\phi\rangle\otimes|\psi\rangle$ is defined by this condition:
$$
\langle ab|\pi\rangle=\langle a|\phi\rangle\langle b|\psi\rangle\quad\forall\quad a\in\Sigma\text{ and }b\in\Gamma
$$
	Alternative notation for tensor products:
$$
|\phi\rangle|\psi\rangle=|\phi\rangle\otimes|\psi\rangle\\
|\phi\otimes\psi\rangle=|\phi\rangle\otimes|\psi\rangle
$$
- Observe the following expression for tensor products of standard basis vectors:
$$
|a\rangle\otimes|b\rangle=|a\rangle|b\rangle=|ab\rangle
$$
	Alternatively, writing $(a,b)$ as an ordered pair rather than a string, we could write:
$$
|a\rangle\otimes|b\rangle = |(a,b)\rangle
$$
	But it is more common to write:
$$
|a\rangle\otimes|b\rangle = |a,b\rangle
$$
- The tensor product of two vectors is bilinear.
	1. Linearity in the first argument:
$$
\begin{aligned} (|\phi_1\rangle + |\phi_2\rangle) \otimes |\psi\rangle &= |\phi_1\rangle \otimes |\psi\rangle + |\phi_2\rangle \otimes |\psi\rangle \\ (\alpha |\phi\rangle) \otimes |\psi\rangle &= \alpha (|\phi\rangle \otimes |\psi\rangle) \end{aligned}
$$
	2. Linearity in the second argument:
$$
\begin{aligned} |\phi\rangle \otimes (|\psi_1\rangle + |\psi_2\rangle) &= |\phi\rangle \otimes |\psi_1\rangle + |\phi\rangle \otimes |\psi_2\rangle \\ |\phi\rangle \otimes (\alpha |\psi\rangle) &= \alpha (|\phi\rangle \otimes |\psi\rangle) \end{aligned}
$$
	Scalars float freely within tensor products:
$$
(\alpha |\phi\rangle) \otimes |\psi\rangle = |\phi\rangle \otimes (\alpha |\psi\rangle) = \alpha (|\phi\rangle \otimes |\psi\rangle) = \alpha |\phi\rangle \otimes |\psi\rangle
$$
- Tensor products generalize to three or more systems.
	If $|\phi_1\rangle,\dots,|\phi_n\rangle$ are vectors, then the tensor product $|\psi\rangle=|\phi_1\rangle\otimes\cdots\otimes|\phi_n\rangle$ is defined by the equation:
$$
\langle a_1 \cdots a_n | \psi \rangle = \langle a_1 | \phi_1 \rangle \cdots \langle a_n | \phi_n \rangle
$$
	Equivalently, the tensor product of three or more vectors can be defined recursively:
$$
|\phi_1\rangle \otimes \cdots \otimes |\phi_n\rangle = (|\phi_1\rangle \otimes \cdots \otimes |\phi_{n-1}\rangle) \otimes |\phi_n\rangle
$$
	The tensor product of three or more vectors is multilinear.