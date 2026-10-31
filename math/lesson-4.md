# Lesson 4 — Zerocheck and lincheck

Lesson 3 showed how sumcheck reduces a hypercube sum to one evaluation. Two important uses of that reduction are **zerocheck**, which tests many pointwise constraints at once, and **lincheck**, which verifies that a claimed vector really is a known linear transformation of a committed witness. Flock combines both protocols in its R1CS proof.

We will first work over $\mathbb{F}_8$ so every calculation is checkable by hand. Flock's constraint values themselves are bits in $\mathbb{F}_2$; its implementation performs challenges and polynomial checks in the much larger extension field $\mathbb{F}_{2^{128}}$. These are different roles for different fields.

## 1. Why a plain sum is not a zerocheck

Suppose a table $p:\{0,1\}^m\to\mathbb{F}$ is meant to be zero at every Boolean input. Checking only

$$
\sum_{\mathbf b\in\{0,1\}^m}p(\mathbf b)=0
$$

does not establish that. In characteristic $2$, nonzero entries can cancel. For example, take

$$
p(x,y)=x+y.
$$

On the Boolean square its values are

| $(x,y)$ | $p(x,y)$ |
|---|---|
| $(0,0)$ | $0$ |
| $(1,0)$ | $1$ |
| $(0,1)$ | $1$ |
| $(1,1)$ | $0$ |

The sum is $0+1+1+0=0$ in characteristic $2$, even though $p(1,0)=1$ and $p(0,1)=1$. A useful check must preserve enough information to detect any nonzero entry.

## 2. Random equality weights turn all entries into one claim

For $\mathbf r=(r_1,\ldots,r_m)\in\mathbb{F}^m$ and a Boolean vector $\mathbf b$, define the equality weight

$$
\mathrm{eq}(\mathbf r,\mathbf b)
=\prod_{i=1}^{m}\begin{cases}
r_i,&b_i=1,\\
1-r_i,&b_i=0.
\end{cases}
$$

This is the Boolean interpolation basis from Lesson 2 evaluated at $\mathbf r$. If $\widetilde p$ is the multilinear extension of the table $p$, its interpolation formula gives

$$
\widetilde p(\mathbf r)
=\sum_{\mathbf b\in\{0,1\}^m}\mathrm{eq}(\mathbf r,\mathbf b)\,p(\mathbf b).
$$

So the prover can establish that all table entries are zero by sumchecking the weighted sum with claimed value zero:

$$
\sum_{\mathbf b\in\{0,1\}^m}\mathrm{eq}(\mathbf r,\mathbf b)p(\mathbf b)\stackrel{?}{=}0.
$$

If every entry is zero, the claim holds for every $\mathbf r$. If at least one entry is nonzero, then $\widetilde p$ is a nonzero multilinear polynomial. A uniformly random $\mathbf r$ makes $\widetilde p(\mathbf r)=0$ with probability at most $m/|\mathbb F|$ by the Schwartz–Zippel bound. Sumcheck then verifies the weighted-sum claim, adding its own soundness error. In practice the field is chosen large enough that these errors are negligible.

### The earlier counterexample is caught

For $p(x,y)=x+y$, its multilinear extension is the same polynomial. Choose $\mathbf r=(X,X+1)\in\mathbb{F}_8^2$. Then

$$
\widetilde p(\mathbf r)=X+(X+1)=1,
$$

so the randomized claim is nonzero and cannot honestly be proved as zero. The weighted sum shows the same calculation term by term. Only $p(1,0)$ and $p(0,1)$ contribute:

$$
\begin{aligned}
\mathrm{eq}((X,X+1),(1,0))&=X\bigl(1-(X+1)\bigr)=X^2,\\
\mathrm{eq}((X,X+1),(0,1))&=(1-X)(X+1)=(X+1)^2=X^2+1,\\
\sum_{(x,y)\in\{0,1\}^2}\mathrm{eq}((X,X+1),(x,y))p(x,y)
&=X^2+(X^2+1)=1.
\end{aligned}
$$

The equality weights prevent the two bad entries from simply cancelling.

## 3. Zerocheck as used for an R1CS constraint

An R1CS instance has three matrices $A,B,C$ and a witness vector $\mathbf z$. Define

$$
\mathbf a=A\mathbf z,\qquad \mathbf b=B\mathbf z,\qquad \mathbf c=C\mathbf z.
$$

The constraint is satisfied when, at every Boolean row index $\mathbf i$,

$$
a_{\mathbf i}b_{\mathbf i}-c_{\mathbf i}=0.
$$

For a Boolean circuit encoded over $\mathbb{F}_2$, the entries $a_{\mathbf i},b_{\mathbf i},c_{\mathbf i}$ are bits. The verifier samples $\mathbf r$ in a larger extension field and the prover runs sumcheck with initial claim zero on the summand

$$
G_{\mathbf r}(\mathbf i)
=\mathrm{eq}(\mathbf r,\mathbf i)
\bigl(\widehat a(\mathbf i)\widehat b(\mathbf i)-\widehat c(\mathbf i)\bigr),
\qquad \mathbf i\in\{0,1\}^m.
$$

At Boolean inputs, the multilinear extensions recover the bit vectors, so the parenthesized term is exactly the R1CS residual at that row. The sum over the Boolean cube is the multilinear extension of the residual table evaluated at $\mathbf r$. If any row is invalid, a random $\mathbf r$ catches it except with the polynomial identity error described above.

After sumcheck reaches a random point $\mathbf r_y$, the verifier checks the final value using the claimed evaluations $v_a=\widehat a(\mathbf r_y)$, $v_b=\widehat b(\mathbf r_y)$, and $v_c=\widehat c(\mathbf r_y)$:

$$
\mathrm{eq}(\mathbf r,\mathbf r_y)(v_av_b-v_c).
$$

The individual degree of this sumcheck expression is at most $3$: the equality factor has degree one in each coordinate, each multilinear extension has degree one in each coordinate, and the product $\widehat a\widehat b$ has degree at most two in each. This degree bound is part of the verifier's checks.

There is a subtle distinction here. The Boolean residual table has a multilinear extension, but $\widehat a\,\widehat b-\widehat c$ need not equal that extension away from Boolean points. They agree on the Boolean cube, which is what makes the weighted sum encode the residual table; the latter polynomial is the convenient low-degree expression whose final value is checked from the separate $a,b,c$ openings.

## 4. Lincheck: bind a vector claim to the witness

Zerocheck ends with claims about $\widehat a,\widehat b,\widehat c$ at a random point. Those values cannot be trusted just because the prover sent them. Lincheck reduces a claim about $\widehat a$ to a claim about the committed witness $\widehat z$, using the known matrix $A$.

Let the Boolean row and column indices be $\mathbf i,\mathbf j\in\{0,1\}^m$. Because $\mathbf a=A\mathbf z$,

$$
\begin{aligned}
\widehat a(\mathbf r_y)
&=\sum_{\mathbf i\in\{0,1\}^m}
  \mathrm{eq}(\mathbf r_y,\mathbf i)a_{\mathbf i}\\
&=\sum_{\mathbf i,\mathbf j\in\{0,1\}^m}
  \mathrm{eq}(\mathbf r_y,\mathbf i)A_{\mathbf i,\mathbf j}z_{\mathbf j}\\
&=\sum_{\mathbf j\in\{0,1\}^m}
  \widehat A(\mathbf r_y,\mathbf j)\widehat z(\mathbf j)\\
&=\sum_{\mathbf x\in\{0,1\}^m}
  \widehat A(\mathbf r_y,\mathbf x)\widehat z(\mathbf x).
\end{aligned}
$$

Here $\widehat A$ is the multilinear extension of the matrix viewed as a table on row-column pairs. On Boolean $\mathbf j$, $\widehat z(\mathbf j)=z_{\mathbf j}$. The verifier and prover can therefore run sumcheck on

$$
h(\mathbf x)=\widehat A(\mathbf r_y,\mathbf x)\widehat z(\mathbf x),
$$

with claimed sum $v_a$. At the end, for a random $\mathbf r_x$, the verifier checks

$$
v_a\stackrel{?}{=}\widehat A(\mathbf r_y,\mathbf r_x)\widehat z(\mathbf r_x)
$$

through the sumcheck transition and an opening of the committed witness polynomial. The matrix evaluation is computable from the public instance; the witness evaluation is authenticated by its polynomial commitment. The same construction applies to $B$ and $C$. Their claims can be batched so they share a sumcheck and a final witness opening.

## 5. A one-variable lincheck by hand

Use $\mathbb{F}_8$ as the challenge field, with the matrix and witness entries embedded from $\mathbb{F}_2$:

$$
A=\begin{pmatrix}1&1\\0&1\end{pmatrix},
\qquad
\mathbf z=\begin{pmatrix}1\\0\end{pmatrix},
\qquad
\mathbf a=A\mathbf z=\begin{pmatrix}1\\0\end{pmatrix}.
$$

Let $y$ index rows and $x$ index columns. The matrix's multilinear extension is

$$
\begin{aligned}
\widehat A(y,x)
&=(1-y)(1-x)+(1-y)x+yx\\
&=1-y+yx.
\end{aligned}
$$

The witness and output extensions are

$$
\widehat z(x)=1-x,\qquad \widehat a(y)=1-y.
$$

Suppose zerocheck has produced the output evaluation claim at $r_y=X$. Directly,

$$
v_a=\widehat a(X)=1-X=1+X.
$$

Lincheck rewrites this as a Boolean sum:

$$
\begin{aligned}
\sum_{x\in\{0,1\}}\widehat A(X,x)\widehat z(x)
&=\widehat A(X,0)\widehat z(0)+\widehat A(X,1)\widehat z(1)\\
&=(1+X)\cdot1+1\cdot0\\
&=1+X=v_a.
\end{aligned}
$$

The prover's round polynomial is

$$
\begin{aligned}
h(t)&=\widehat A(X,t)\widehat z(t)\\
&=(1+X+Xt)(1-t)\\
&=(1+X+Xt)(1+t)\\
&=1+X+t+Xt^2.
\end{aligned}
$$

The verifier checks

$$
h(0)+h(1)=(1+X)+0=1+X=v_a.
$$

For a hand trace, take the challenge $r_x=X+1$. The sumcheck message gives

$$
h(X+1)=1.
$$

The verifier independently computes the final factors:

$$
\begin{aligned}
\widehat A(X,X+1)&=1+X+X(X+1)=1+X^2,\\
\widehat z(X+1)&=1-(X+1)=X,\\
\widehat A(X,X+1)\widehat z(X+1)
&=(1+X^2)X=X+X^3=X+(X+1)=1.
\end{aligned}
$$

The final value matches the round polynomial. This is the lincheck reduction in a complete one-round example.

## 6. How the two checks fit into Flock

At a high level, the Flock paper's baseline protocol proceeds as follows:

1. The prover commits to the Boolean multilinear extension of the witness trace $\mathbf z$.
2. **Zerocheck** reduces the pointwise R1CS equations $\mathbf a\circ\mathbf b=\mathbf c$ to a randomized sumcheck claim.
3. **Lincheck** reduces the resulting evaluations of $\widehat a,\widehat b,\widehat c$ to evaluations of the committed $\widehat z$ through the public matrices $A,B,C$.
4. The verifier checks public-input consistency and opens the required trace evaluations through the multilinear polynomial commitment scheme.

Flock's target workload is batches of the same Boolean computation. The paper stacks $K$ instances using block-diagonal matrices such as $I_K\otimes A_0$. Their structure lets the lincheck factor out the batch dimension, so the expensive matrix checks work on the small per-instance matrices while the witness is folded across the batch. The paper reports that this makes lincheck comparatively cheap and leaves zerocheck as a main protocol bottleneck.

The paper describes an interactive-oracle proof for R1CS over $\mathbb{F}_2$ with multilinear extensions and challenges over a large extension field. The current implementation uses $\mathbb{F}_{2^{128}}$ and a Ligerito-based multilinear commitment construction; ring-switching adapts the commitment machinery to Boolean-valued witness tables. The optimizations in the implementation—such as batching, univariate skip, and specialized challenge choices—are refinements on top of the generic checks derived above.

### Important distinction: Flock is currently not zero-knowledge

Although Flock is often discussed alongside ZK systems, its paper states that the current system provides succinctness but **not zero-knowledge**. Its commitment and sumcheck machinery establish a succinct argument of correct computation, but do not by themselves hide the witness. This lesson explains its checks, not a privacy guarantee. The authors note that zero-knowledge may be added using separate techniques; that is additional protocol work, not a property to assume today.

## Exercises

1. For the table $p(x,y)=xy$, compute the weighted sum $\sum_{(x,y)}\mathrm{eq}((X,X+1),(x,y))p(x,y)$ and verify it equals $X(X+1)$.
2. In the lincheck example, compute $\widehat A(X,0)$ and $\widehat A(X,1)$ from the four matrix entries rather than from the simplified formula.
3. Explain why a lincheck is necessary even after zerocheck has produced values $v_a,v_b,v_c$.
4. In the Flock R1CS relation, identify which values are bits in $\mathbb{F}_2$ and which challenges/evaluations lie in the extension field.

## Further reading

- [Bünz, Rothblum, and Wang, “Flock: Fast Proving for Batch Boolean Computations”](https://arxiv.org/abs/2607.27491), especially §§2.3, 3, and 4.1.
- [The Flock implementation](https://github.com/succinctlabs/flock), whose README describes its R1CS-over-$\mathbb{F}_2$ protocol, zerocheck, lincheck, and Ligerito-based PCS.
