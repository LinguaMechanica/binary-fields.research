# Lesson 2 — Multilinear interpolation on the Boolean hypercube

In Lesson 1, we evaluated a polynomial over $\mathbb{F}_8$ at the four Boolean points and summed those values. Now we reverse the viewpoint: given only the values on the Boolean points, how do we recover the polynomial?

This construction is called the **multilinear extension** of a function on the Boolean hypercube. It is the bridge between a finite table of values and polynomial evaluation at arbitrary field points, which is used throughout sumcheck-based proof systems.

## 1. The two-variable case

A polynomial that is multilinear in $x$ and $y$ has the form

$$
f(x,y)=a+bx+cy+dxy,
\qquad a,b,c,d\in\mathbb{F}_8.
$$

The degree in each variable is at most one. Evaluate at the four Boolean points, where each coordinate is either $0$ or $1$:

$$
\begin{aligned}
f(0,0)&=a,\\
f(1,0)&=a+b,\\
f(0,1)&=a+c,\\
f(1,1)&=a+b+c+d.
\end{aligned}
$$

Write these four values as

$$
v_{00}=f(0,0),\quad v_{10}=f(1,0),\quad
v_{01}=f(0,1),\quad v_{11}=f(1,1).
$$

We can solve for the coefficients one at a time. Since $a=v_{00}$,

$$
b=v_{10}-v_{00},\qquad c=v_{01}-v_{00},
$$

and

$$
d=v_{11}-v_{10}-v_{01}+v_{00}.
$$

In $\mathbb{F}_8$, subtraction equals addition because the field has characteristic $2$. Therefore the same formulas become

$$
\boxed{\begin{aligned}
a&=v_{00},\\
b&=v_{10}+v_{00},\\
c&=v_{01}+v_{00},\\
d&=v_{11}+v_{10}+v_{01}+v_{00}.
\end{aligned}}
$$

These equations show directly that the four Boolean values determine all four coefficients.

## 2. Lagrange basis functions for Boolean inputs

There is a compact formula that generalizes to any number of variables. For a Boolean bit $b\in\{0,1\}$, define its one-variable selector at $x$ by

$$
\lambda_0(x)=1-x,\qquad \lambda_1(x)=x.
$$

At the Boolean inputs, these functions select one point:

| $b$ | $\lambda_b(0)$ | $\lambda_b(1)$ |
|---|---|---|
| $0$ | $1$ | $0$ |
| $1$ | $0$ | $1$ |

For a Boolean vector $\mathbf{b}=(b_1,\ldots,b_n)\in\{0,1\}^n$, define

$$
\chi_{\mathbf b}(x_1,\ldots,x_n)
=\prod_{i=1}^{n}\lambda_{b_i}(x_i)
=\prod_{i=1}^{n}\bigl(b_i x_i+(1-b_i)(1-x_i)\bigr).
$$

The factor form on the right makes the two cases explicit: it is $x_i$ when $b_i=1$ and $1-x_i$ when $b_i=0$. At a Boolean input $\mathbf u\in\{0,1\}^n$,

$$
\chi_{\mathbf b}(\mathbf u)=
\begin{cases}
1,&\mathbf u=\mathbf b,\\
0,&\mathbf u\ne\mathbf b.
\end{cases}
$$

So $\chi_{\mathbf b}$ is $1$ at its own Boolean vertex and $0$ at every other Boolean vertex. This is the interpolation basis property.

Given any table of values $T:\{0,1\}^n\to\mathbb{F}_8$, its multilinear extension is

$$
\boxed{\widetilde T(x_1,\ldots,x_n)
=\sum_{\mathbf b\in\{0,1\}^n}T(\mathbf b)\,\chi_{\mathbf b}(x_1,\ldots,x_n).}
$$

At a Boolean point $\mathbf u$, every term vanishes except the term indexed by $\mathbf u$, so $\widetilde T(\mathbf u)=T(\mathbf u)$. This formula uses only addition and multiplication in the field; the values $0$ and $1$ are distinct in $\mathbb{F}_8$, so no division is required.

## 3. The two-variable formula expanded

For two variables, the four basis functions are

$$
\begin{aligned}
\chi_{00}(x,y)&=(1-x)(1-y),\\
\chi_{10}(x,y)&=x(1-y),\\
\chi_{01}(x,y)&=(1-x)y,\\
\chi_{11}(x,y)&=xy.
\end{aligned}
$$

Thus the polynomial interpolating four values is

$$
f(x,y)=v_{00}(1-x)(1-y)+v_{10}x(1-y)
+v_{01}(1-x)y+v_{11}xy.
$$

For example, at $(x,y)=(1,0)$ the basis values are

$$
\chi_{00}(1,0)=0,\quad \chi_{10}(1,0)=1,
\quad \chi_{01}(1,0)=0,\quad \chi_{11}(1,0)=0,
$$

so the formula returns $f(1,0)=v_{10}$ as required.

## 4. Work through the Lesson 1 example

Recall the values from Lesson 1:

$$
\begin{array}{c|c}
(x,y)&f(x,y)\\\hline
(0,0)&X\\
(1,0)&1\\
(0,1)&X^2+X\\
(1,1)&X^2
\end{array}
$$

Use the coefficient formulas, doing every addition in $\mathbb{F}_8$:

$$
\begin{aligned}
a&=v_{00}=X,\\
b&=v_{10}+v_{00}=1+X=X+1,\\
c&=v_{01}+v_{00}=(X^2+X)+X=X^2,\\
d&=v_{11}+v_{10}+v_{01}+v_{00}\\
 &=X^2+1+(X^2+X)+X\\
 &=(X^2+X^2)+(X+X)+1\\
 &=1.
\end{aligned}
$$

Therefore the unique multilinear polynomial is

$$
f(x,y)=X+(X+1)x+X^2y+xy.
$$

Check its four vertex values:

$$
\begin{aligned}
f(0,0)&=X,\\
f(1,0)&=X+(X+1)=1,\\
f(0,1)&=X+X^2=X^2+X,\\
f(1,1)&=X+(X+1)+X^2+1=X^2.
\end{aligned}
$$

For the last line, the two copies of $X$ cancel, as do the two copies of $1$.

## 5. Evaluating away from the Boolean cube

The extension agrees with the table at Boolean points, but it is also defined at every point in $\mathbb{F}_8^n$. For instance, evaluate at $(X,0)$, which is not a Boolean vertex because $X\notin\{0,1\}$:

$$
\begin{aligned}
f(X,0)&=X+(X+1)X+X^2\cdot0+X\cdot0\\
&=X+(X^2+X)\\
&=X^2.
\end{aligned}
$$

The same value follows directly from the interpolation formula. Since $y=0$, the terms indexed by $(0,1)$ and $(1,1)$ vanish:

$$
\begin{aligned}
f(X,0)&=v_{00}(1-X)+v_{10}X\\
&=X(1-X)+1\cdot X\\
&=X(1+X)+X\\
&=(X+X^2)+X\\
&=X^2.
\end{aligned}
$$

We used $-X=X$ in characteristic $2$. This off-cube evaluation is not another table lookup; it is the value prescribed by the unique multilinear polynomial extending the table.

## 6. Why the extension is unique

The basis formula constructs an extension. To see why no other multilinear polynomial can have the same Boolean values, it is enough to show that a multilinear polynomial that is zero at every Boolean point must be the zero polynomial.

We can prove this by induction on the number of variables. The zero-variable case is a constant, so the claim is immediate. For $n$ variables, write a multilinear polynomial as

$$
g(x_1,\ldots,x_n)=A(x_1,\ldots,x_{n-1})+x_nB(x_1,\ldots,x_{n-1}),
$$

where $A$ and $B$ are multilinear. If $g$ is zero at every Boolean point, setting $x_n=0$ shows that $A$ is zero on the $(n-1)$-dimensional Boolean cube. By the induction hypothesis, $A$ is the zero polynomial. Setting $x_n=1$ then shows that $A+B=B$ is zero on that cube, so the induction hypothesis makes $B$ the zero polynomial as well. Hence $g=0$.

If two multilinear polynomials agree on every Boolean vertex, their difference vanishes on every vertex; by this argument the difference is zero. Therefore the interpolant is unique.

## 7. Connection to sumcheck

The Lesson 1 table had Boolean sum

$$
\sum_{(u,v)\in\{0,1\}^2} f(u,v)=1.
$$

Multilinear interpolation lets us regard this table as a polynomial over $\mathbb{F}_8$, while retaining the table values at Boolean inputs. In sumcheck, the prover claims a sum over all Boolean inputs and sends low-degree polynomials round by round; the verifier uses random field challenges to reduce the claim to a final polynomial evaluation. The next lesson will work through that protocol completely on a small example.

## Exercises

1. A two-variable Boolean table has values $v_{00}=1$, $v_{10}=X$, $v_{01}=X+1$, and $v_{11}=X^2$. Find $a,b,c,d$ for its multilinear polynomial $a+bx+cy+dxy$.
2. Write the four two-variable basis functions $\chi_{00},\chi_{10},\chi_{01},\chi_{11}$ and verify their values at $(0,0)$.
3. For the Lesson 1 polynomial, compute $f(0,X)$, showing each field operation.
4. Explain why the multilinear extension is unique even though $\mathbb{F}_8^2$ contains more than four points.

## Markdown math

This file uses GitHub-compatible math delimiters: `$...$` for inline math and `$$...$$` for display math. GitHub renders the equations in the `.md` preview; a plain text editor will show the delimiters literally.
