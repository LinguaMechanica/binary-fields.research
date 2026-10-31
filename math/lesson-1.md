# Lesson 1 — Arithmetic in $\mathrm{GF}(2^3)$

We will build the smallest useful example of a binary extension field by hand. The point of doing this first is to make later polynomial protocols concrete: every coefficient and every intermediate result can be checked with three-bit arithmetic.

Throughout this lesson, use the irreducible polynomial

$$
p(X)=X^3+X+1.
$$

We define

$$
\mathbb{F}_8 = \mathbb{F}_2[X]/(X^3+X+1),
\qquad \mathbb{F}_2=\{0,1\}.
$$

In $\mathbb{F}_2$, addition and multiplication are modulo $2$. In the quotient, polynomial expressions are reduced modulo $p(X)$.

## 1. Why are there eight elements?

Every polynomial has a unique remainder of degree less than $3$ after division by $p(X)$. Thus each field element has a unique form

$$
a_0+a_1X+a_2X^2, \qquad a_0,a_1,a_2\in\{0,1\}.
$$

There are two choices for each of three coefficients, so $|\mathbb{F}_8|=2^3=8$. The relation imposed by the quotient is

$$
X^3+X+1=0, \qquad\text{so}\qquad X^3=X+1,
$$

where subtraction equals addition in characteristic $2$.

| Coefficients $(a_2a_1a_0)$ | Polynomial | Hex-like label |
|---|---|---|
| `000` | $0$ | $0$ |
| `001` | $1$ | $1$ |
| `010` | $X$ | $2$ |
| `011` | $X+1$ | $3$ |
| `100` | $X^2$ | $4$ |
| `101` | $X^2+1$ | $5$ |
| `110` | $X^2+X$ | $6$ |
| `111` | $X^2+X+1$ | $7$ |

The three-bit label records coefficients; it is not an instruction to use ordinary integer multiplication. For example, `111` means $X^2+X+1$.

## 2. Why is the modulus irreducible?

A cubic over a field is reducible if and only if it has a root in that field. The only possible roots in $\mathbb{F}_2$ are $0$ and $1$. We have

$$
p(0)=1\ne0,\qquad p(1)=1+1+1=1\ne0\quad\text{in }\mathbb{F}_2.
$$

So $p$ has no root and is irreducible. Quotienting $\mathbb{F}_2[X]$ by an irreducible polynomial gives a field, which is why every nonzero element here has a multiplicative inverse.

## 3. Addition

Add coefficients modulo $2$. Equivalently, XOR the bit labels. Equal terms cancel because $1+1=0$.

For example,

$$
(X^2+X+1)+(X^2+1)
= (1+1)X^2+X+(1+1)
=X.
$$

In coefficient labels this is `111 XOR 101 = 010`.

## 4. Multiplication and reduction

Multiply as polynomials over $\mathbb{F}_2$, then replace every occurrence of $X^3$ using $X^3=X+1$ until the degree is below $3$.

Example 1:

$$
\begin{aligned}
X(X^2+1)&=X^3+X\\
&=(X+1)+X\\
&=1.
\end{aligned}
$$

Thus $X^{-1}=X^2+1$.

Example 2:

$$
\begin{aligned}
(X+1)(X^2+1)
&=X^3+X^2+X+1\\
&=(X+1)+X^2+X+1\\
&=X^2.
\end{aligned}
$$

Example 3 (showing cancellation during reduction):

$$
\begin{aligned}
(X^2+X+1)^2
&=X^4+X^2+1\\
&=X(X^3)+X^2+1\\
&=X(X+1)+X^2+1\\
&=X^2+X+X^2+1\\
&=X+1.
\end{aligned}
$$

The first equality uses characteristic $2$: when squaring, the cross terms have coefficient $2=0$.

## 5. Powers and inverses

Powers of $X$ make the inverses easy to verify:

$$
\begin{array}{c|c}
k & X^k\\\hline
0&1\\
1&X\\
2&X^2\\
3&X+1\\
4&X^2+X\\
5&X^2+X+1\\
6&X^2+1\\
7&1
\end{array}
$$

For instance, $X^3=X+1$ by the modulus relation, and

$$
X^4=X(X+1)=X^2+X,\qquad
X^5=X(X^2+X)=X^3+X^2=X^2+X+1.
$$

The nonzero elements form a multiplicative group of order $7$. The power list shows that every nonzero element is $X^k$ for some $k\in\{0,1,\ldots,6\}$, with $X^7=1$. Thus $(X^k)^{-1}=X^{7-k}$, with exponents reduced modulo $7$. The complete inverse table is:

| $a$ | $a^{-1}$ |
|---|---|
| $1$ | $1$ |
| $X$ | $X^2+1$ |
| $X^2$ | $X^2+X+1$ |
| $X+1$ | $X^2+X$ |
| $X^2+X$ | $X+1$ |
| $X^2+X+1$ | $X^2$ |
| $X^2+1$ | $X$ |

As a check on one less obvious entry:

$$
X^2(X^2+X+1)=X^4+X^3+X^2=(X^2+X)+(X+1)+X^2=1.
$$

## 6. Complete multiplication table

For a compact table, label the nonzero elements by powers of $X$:

$$
\begin{array}{lll}
e_0=1, & e_1=X, & e_2=X^2,\\
e_3=X+1, & e_4=X^2+X, & e_5=X^2+X+1,\\
e_6=X^2+1. &&
\end{array}
$$

Since $e_i=X^i$ and $X^7=1$, the product rule is

$$
e_i e_j=e_{(i+j)\bmod 7}.
$$

The table includes the zero row and column:

| $\times$ | $0$ | $e_0$ | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ | $e_6$ |
|---|---|---|---|---|---|---|---|---|
| $0$ | $0$ | $0$ | $0$ | $0$ | $0$ | $0$ | $0$ | $0$ |
| $e_0$ | $0$ | $e_0$ | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ | $e_6$ |
| $e_1$ | $0$ | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ | $e_6$ | $e_0$ |
| $e_2$ | $0$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ | $e_6$ | $e_0$ | $e_1$ |
| $e_3$ | $0$ | $e_3$ | $e_4$ | $e_5$ | $e_6$ | $e_0$ | $e_1$ | $e_2$ |
| $e_4$ | $0$ | $e_4$ | $e_5$ | $e_6$ | $e_0$ | $e_1$ | $e_2$ | $e_3$ |
| $e_5$ | $0$ | $e_5$ | $e_6$ | $e_0$ | $e_1$ | $e_2$ | $e_3$ | $e_4$ |
| $e_6$ | $0$ | $e_6$ | $e_0$ | $e_1$ | $e_2$ | $e_3$ | $e_4$ | $e_5$ |

For example, row $e_4$, column $e_5$ gives $e_2$, because

$$
e_4e_5=X^4X^5=X^9=X^2,
$$

using $X^7=1$. The table is symmetric because multiplication in a field is commutative.

## 7. Connection to multilinear polynomials

The field supplies coefficients and evaluation arithmetic; *multilinearity* describes the polynomial's structure. For two variables, a multilinear polynomial over $\mathbb{F}_8$ has the form

$$
f(x,y)=a+bx+cy+dxy, \qquad a,b,c,d\in\mathbb{F}_8.
$$

Each variable has degree at most one. The term $xy$ is allowed, but $x^2$ and $y^2$ are not. The two-dimensional Boolean hypercube is

$$
\{0,1\}^2=\{(0,0),(0,1),(1,0),(1,1)\}.
$$

The input coordinates are the field elements $0$ and $1$, and evaluations and sums take place in $\mathbb{F}_8$.

Take the concrete polynomial

$$
f(x,y)=X+(X+1)x+X^2y+xy.
$$

Its values on the Boolean square are:

| Point | Evaluation | Result |
|---|---|---|
| $(0,0)$ | $X$ | $X$ |
| $(1,0)$ | $X+(X+1)$ | $1$ |
| $(0,1)$ | $X+X^2$ | $X^2+X$ |
| $(1,1)$ | $X+(X+1)+X^2+1$ | $X^2$ |

At $(1,1)$, the cancellation is explicit:

$$
\begin{aligned}
f(1,1)&=X+(X+1)+X^2+1\\
&=(X+X)+(1+1)+X^2\\
&=X^2.
\end{aligned}
$$

Summing over all Boolean inputs gives

$$
\begin{aligned}
S&=\sum_{(x,y)\in\{0,1\}^2} f(x,y)\\
 &=X+1+(X^2+X)+X^2\\
 &=1.
\end{aligned}
$$

The $X$ terms cancel each other, as do the $X^2$ terms. This small hypercube sum is the kind of claim that sumcheck verifies: for a large polynomial, the verifier can check the claim without evaluating every hypercube point.

## 8. Hand procedure for a product

To multiply two elements reliably:

1. Write each as a polynomial of degree at most $2$.
2. Expand using distributivity.
3. Combine like terms modulo $2$; duplicate terms cancel.
4. Replace $X^3$ with $X+1$ and $X^4$ with $X^2+X$ (the latter follows by multiplying $X^3=X+1$ by $X$).
5. Combine terms again.

For example, compute $(X^2+1)(X+1)$:

$$
\begin{aligned}
(X^2+1)(X+1)&=X^3+X^2+X+1\\
&=(X+1)+X^2+X+1\\
&=X^2.
\end{aligned}
$$

## 9. Why this field is a useful starting point

This field is small enough that a full addition or multiplication can be audited by hand, yet it has genuinely nontrivial extension-field arithmetic. Later, we can use its elements as values and coefficients in multilinear polynomials and sumcheck examples. A practical implementation often stores $a_0+a_1X+a_2X^2$ as the bit vector $(a_2a_1a_0)$; addition is XOR, while multiplication is carryless polynomial multiplication followed by reduction modulo `0b1011`, the coefficient encoding of $X^3+X+1$.

## Exercises

1. Compute $(X^2+X)(X+1)$ by expansion and reduction.
2. Find $(X^2+1)+(X+1)$ in polynomial form and as a three-bit label.
3. Verify that $X^2+X+1$ is the inverse of $X^2$.
4. Show from $X^3=X+1$ that $X^7=1$.

## GitHub Markdown note

GitHub renders LaTeX-style math in Markdown files. Use `$...$` for inline expressions and `$$...$$` on separate lines for display equations. In a repository, open the `.md` file on GitHub to preview the rendered result before committing; in a local editor, use a Markdown preview that supports math as a second check. The literal delimiters may remain visible in a plain text editor—that is expected.

## Next step

Next, derive multilinear interpolation from these Boolean evaluations. Then we can work through a complete sumcheck protocol, with the prover's univariate polynomial in each round and the verifier's checks at random field challenges. We will keep the sequence mathematical and use explicit $\mathbb{F}_8$ calculations throughout.
