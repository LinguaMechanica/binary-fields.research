# Lesson 1 — Arithmetic in \\(\mathrm{GF}(2^3)\\)

We will proceed in mathematical order: first construct the field and verify its arithmetic; then use it as the coefficient field for multilinear polynomials; finally, work through a complete sumcheck protocol by hand. We will not introduce Binius- or Flock-specific machinery until the underlying mathematics is established.

Our starting point is the field

\\[ \mathbb F_8=\mathrm{GF}(2^3) =\mathbb F_2[X]/(X^3+X+1). \\]

Here, \\(\mathbb F_2=\\{0,1\\}\\) is the field with arithmetic modulo \\(2\\). The polynomial \\(X^3+X+1\\) is our chosen irreducible polynomial.

## 1. Why does this field have eight elements?

The construction uses polynomials with coefficients in \\(\mathbb F_2\\), with two polynomials considered equivalent when their difference is divisible by

\\[ p(X)=X^3+X+1. \\]

Equivalently, we perform polynomial arithmetic modulo \\(p(X)\\).

The crucial observation is that every polynomial has a unique remainder of degree less than three after division by \\(p(X)\\). Therefore, every field element has a unique representation

\\[ a_0+a_1X+a_2X^2, \qquad a_i\in\\{0,1\\}. \\]

There are three independent binary coefficients, giving

\\[ |\mathbb F_8|=2\cdot2\cdot2=2^3=8. \\]

The eight elements are:

| Binary coefficients \\(a_2a_1a_0\\) | Polynomial    | Label         |
| ----------------------------------- | ------------- | ------------- |
| 000                                 | \\(0\\)       | \\(0\\)       |
| 001                                 | \\(1\\)       | \\(1\\)       |
| 010                                 | \\(X\\)       | \\(X\\)       |
| 011                                 | \\(X+1\\)     | \\(X+1\\)     |
| 100                                 | \\(X^2\\)     | \\(X^2\\)     |
| 101                                 | \\(X^2+1\\)   | \\(X^2+1\\)   |
| 110                                 | \\(X^2+X\\)   | \\(X^2+X\\)   |
| 111                                 | \\(X^2+X+1\\) | \\(X^2+X+1\\) |

The binary representation is simply a convenient way to store the three coefficients. It is not an integer representation with ordinary integer arithmetic.

For example, the bit pattern `111` represents \\(X^2+X+1\\), not the integer seven for purposes of field multiplication.

## 2. Why is \\(X^3+X+1\\) irreducible?

A degree-three polynomial over a field is irreducible if and only if it has no root in that field. If it had a root, it would have a linear factor; conversely, any nontrivial factorization of a cubic must include a linear factor.

We only need to test \\(0\\) and \\(1\\), the two elements of \\(\mathbb F_2\\).

At \\(X=0\\),

\\[ p(0)=0+0+1=1. \\]

At \\(X=1\\), with arithmetic modulo two,

\\[ p(1)=1+1+1=1. \\]

Neither value is zero. Thus \\(p(X)\\) has no root in \\(\mathbb F_2\\) and is irreducible.

This matters because the quotient by an irreducible polynomial is a field: every nonzero element has a multiplicative inverse. A reducible polynomial would produce a quotient ring containing nonzero elements without inverses.

## 3. Addition: coefficient-wise XOR

Addition is straightforward because the coefficient field is \\(\mathbb F_2\\):

\\[ 0+0=0,\quad 0+1=1,\quad 1+1=0. \\]

Consequently, adding field elements is bitwise XOR. For example,

\\[ \begin{aligned} (X^2+X+1)+(X^2+1) &=(X^2+X^2)+X+(1+1)\\\ &=0+X+0\\\ &=X. \end{aligned} \\]

In bit representation,

\\[ 111\oplus101=010. \\]

There is no carrying, and every element is its own additive inverse:

\\[ a+a=0 \\]

for all \\(a\in\mathbb F_8\\). This is the defining characteristic-two property we will encounter repeatedly in binary-field proof systems.

## 4. Multiplication: polynomial multiplication followed by reduction

Multiplication is where the extension-field construction becomes important.

From our defining relation,

\\[ X^3+X+1=0, \\]

and because the characteristic is two, we obtain

\\[ \boxed{X^3=X+1}. \\]

Every power of \\(X\\) of degree three or greater can be reduced using this identity.

Let's calculate the successive powers explicitly.

\\[ \begin{aligned} X^3 &= X+1,\\\ X^4 &= X(X+1)=X^2+X,\\\ X^5 &= X(X^2+X)=X^3+X^2\\\     &=X^2+X+1,\\\ X^6 &= X(X^2+X+1)=X^3+X^2+X\\\     &=X^2+1,\\\ X^7 &= X(X^2+1)=X^3+X\\\     &=(X+1)+X=1. \end{aligned} \\]

Each equality uses ordinary polynomial multiplication first and reduction modulo \\(p(X)\\) afterward.

This gives a useful ordering of all seven nonzero elements:

| Exponent \\(i\\) | \\(X^i\\) reduced | Binary |
| ---------------- | ----------------- | ------ |
| 0                | \\(1\\)           | 001    |
| 1                | \\(X\\)           | 010    |
| 2                | \\(X^2\\)         | 100    |
| 3                | \\(X+1\\)         | 011    |
| 4                | \\(X^2+X\\)       | 110    |
| 5                | \\(X^2+X+1\\)     | 111    |
| 6                | \\(X^2+1\\)       | 101    |
| 7                | \\(1\\)           | 001    |

The fact that \\(X^7=1\\) will make the multiplication table especially convenient.

### A complete worked multiplication

Consider

\\[ (X^2+X+1)(X^2+1). \\]

First expand, without reducing:

\\[ \begin{aligned} & (X^2+X+1)(X^2+1)\\\ &=X^4+X^2+X^3+X+X^2+1\\\ &=X^4+X^3+X+1. \end{aligned} \\]

The two \\(X^2\\) terms cancel because \\(X^2+X^2=0\\).

Now reduce:

\\[ \begin{aligned} X^4+X^3+X+1 &=(X^2+X)+(X+1)+X+1\\\ &=X^2+(X+X+X)+(1+1)\\\ &=X^2+X. \end{aligned} \\]

Therefore,

\\[ \boxed{(X^2+X+1)(X^2+1)=X^2+X}. \\]

Notice the distinction from integer arithmetic: the product is computed in a quotient polynomial ring, and cancellation occurs modulo two.

## 5. The complete multiplication table

For a compact, verifiable table, define the following labels:

\\[ \begin{array}{lll} e_0=1, & e_1=X, & e_2=X^2,\\\ e_3=X+1, & e_4=X^2+X, & e_5=X^2+X+1,\\\ e_6=X^2+1. && \end{array} \\]

Since \\(e_i=X^i\\) and \\(X^7=1\\), multiplication of nonzero elements follows

\\[ \boxed{e_i e_j=e\_{(i+j)\bmod 7}}. \\]

This exponent rule is a consequence of the polynomial reduction we just performed; it is not an assumption about the field.

Here is the full table. The zero row and column are included.

| \\(\times\\) | \\(0\\) | \\(e_0\\) | \\(e_1\\) | \\(e_2\\) | \\(e_3\\) | \\(e_4\\) | \\(e_5\\) | \\(e_6\\) |
| ------------ | ------- | --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| \\(0\\)      | 0       | 0         | 0         | 0         | 0         | 0         | 0         | 0         |
| \\(e_0\\)    | 0       | \\(e_0\\) | \\(e_1\\) | \\(e_2\\) | \\(e_3\\) | \\(e_4\\) | \\(e_5\\) | \\(e_6\\) |
| \\(e_1\\)    | 0       | \\(e_1\\) | \\(e_2\\) | \\(e_3\\) | \\(e_4\\) | \\(e_5\\) | \\(e_6\\) | \\(e_0\\) |
| \\(e_2\\)    | 0       | \\(e_2\\) | \\(e_3\\) | \\(e_4\\) | \\(e_5\\) | \\(e_6\\) | \\(e_0\\) | \\(e_1\\) |
| \\(e_3\\)    | 0       | \\(e_3\\) | \\(e_4\\) | \\(e_5\\) | \\(e_6\\) | \\(e_0\\) | \\(e_1\\) | \\(e_2\\) |
| \\(e_4\\)    | 0       | \\(e_4\\) | \\(e_5\\) | \\(e_6\\) | \\(e_0\\) | \\(e_1\\) | \\(e_2\\) | \\(e_3\\) |
| \\(e_5\\)    | 0       | \\(e_5\\) | \\(e_6\\) | \\(e_0\\) | \\(e_1\\) | \\(e_2\\) | \\(e_3\\) | \\(e_4\\) |
| \\(e_6\\)    | 0       | \\(e_6\\) | \\(e_0\\) | \\(e_1\\) | \\(e_2\\) | \\(e_3\\) | \\(e_4\\) | \\(e_5\\) |

For example, the entry at row \\(e_4\\), column \\(e_5\\) is \\(e_2\\), because

\\[ e_4e_5=X^4X^5=X^9=X^2 \\]

using \\(X^7=1\\).

You can verify any other entry in exactly the same way. The table is symmetric because field multiplication is commutative.

## 6. Multiplicative inverses

An inverse of \\(a\neq0\\) is an element \\(a^{-1}\\) such that

\\[ aa^{-1}=1. \\]

Using the exponent rule, \\(e_i^{-1}=e\_{(7-i)\bmod7}\\) for \\(i\neq0\\).

| Element           | Inverse           | Verification             |
| ----------------- | ----------------- | ------------------------ |
| \\(1\\)           | \\(1\\)           | \\(1\cdot1=1\\)          |
| \\(X\\)           | \\(X^6=X^2+1\\)   | \\(X\cdot X^6=X^7=1\\)   |
| \\(X^2\\)         | \\(X^5=X^2+X+1\\) | \\(X^2\cdot X^5=X^7=1\\) |
| \\(X+1=X^3\\)     | \\(X^4=X^2+X\\)   | \\(X^3\cdot X^4=X^7=1\\) |
| \\(X^2+X=X^4\\)   | \\(X^3=X+1\\)     | \\(X^4\cdot X^3=X^7=1\\) |
| \\(X^2+X+1=X^5\\) | \\(X^2\\)         | \\(X^5\cdot X^2=X^7=1\\) |
| \\(X^2+1=X^6\\)   | \\(X\\)           | \\(X^6\cdot X=X^7=1\\)   |

Zero has no multiplicative inverse.

The important general fact is that the seven nonzero elements form a multiplicative group of order seven. In this particular field, \\(X\\) generates the entire group.

## 7. The connection to multilinear polynomials

Now we can connect field arithmetic to the polynomial machinery used in sumcheck protocols.

The key distinction is that the field supplies the coefficients and evaluation arithmetic; multilinearity describes the polynomial's structure.

For two variables, a multilinear polynomial over \\(\mathbb F_8\\) has the form

\\[ f(x,y)=a+bx+cy+dxy, \qquad a,b,c,d\in\mathbb F_8. \\]

Each variable has degree at most one. The polynomial can contain the product \\(xy\\), but not \\(x^2\\) or \\(y^2\\).

The Boolean hypercube in two dimensions is

\\[ \\{0,1\\}^2=\\{(0,0),(0,1),(1,0),(1,1)\\}. \\]

Although the polynomial's coefficients belong to \\(\mathbb F_8\\), its Boolean input coordinates are the field elements \\(0\\) and \\(1\\). All evaluations and sums are performed in \\(\mathbb F_8\\).

### A concrete example

Choose

\\[ f(x,y)=X+(X+1)x+X^2y+xy. \\]

Evaluate it at the four Boolean points.

| Point       | Evaluation          | Result      |
| ----------- | ------------------- | ----------- |
| \\((0,0)\\) | \\(X\\)             | \\(X\\)     |
| \\((1,0)\\) | \\(X+(X+1)\\)       | \\(1\\)     |
| \\((0,1)\\) | \\(X+X^2\\)         | \\(X^2+X\\) |
| \\((1,1)\\) | \\(X+(X+1)+X^2+1\\) | \\(X^2\\)   |

For example, at \\((1,1)\\),

\\[ \begin{aligned} f(1,1) &=X+(X+1)+X^2+1\\\ &=(X+X)+(1+1)+X^2\\\ &=X^2. \end{aligned} \\]

Now sum the polynomial over the Boolean hypercube:

\\[ \begin{aligned} S&=\sum\_{(x,y)\in\\{0,1\\}^2}f(x,y)\\\  &=X+1+(X^2+X)+X^2\\\  &=1. \end{aligned} \\]

The \\(X\\) terms cancel, as do the \\(X^2\\) terms. The result is \\(1\\), an element of \\(\mathbb F_8\\).

This is a small example of the kind of sum that sumcheck proves. In a real proving system, the polynomial may encode a much larger computation, but the verifier can check the claimed sum without evaluating every point in the hypercube.

## Where we go next

The next mathematical step is to derive the multilinear interpolation formula from these Boolean evaluations. That will let us express a polynomial using its values on the hypercube, rather than its coefficients.

From there, we can construct a sumcheck example in which the prover sends a univariate polynomial in each round, and the verifier checks the successive claims using random field challenges.

Before proceeding, try this one calculation:

\\[ \boxed{(X^2+X)(X+1)=?} \\]

Expand the product, reduce \\(X^3\\) using \\(X^3=X+1\\), and simplify every coefficient modulo two. This checks the essential operation underlying all the later examples.
