# Lesson 3 — A complete sumcheck protocol over $\mathbb{F}_8$

Lesson 2 showed how Boolean-cube values determine a multilinear polynomial. Now we use that polynomial in a complete two-round sumcheck. The verifier will check a claimed sum over four points by receiving two small univariate polynomials and making two field evaluations.

We use $\mathbb{F}_8=\mathbb{F}_2[X]/(X^3+X+1)$ from Lesson 1, so throughout

$$
X^3=X+1,\qquad X^4=X^2+X,\qquad X^7=1.
$$

Here uppercase $X$ denotes the field element represented by the polynomial generator; lowercase $x,y,t$ denote polynomial variables.

## 1. The claim

Use the Lesson 1 polynomial

$$
f(x,y)=X+(X+1)x+X^2y+xy.
$$

Its Boolean-cube values are

| $(x,y)$ | $f(x,y)$ |
|---|---|
| $(0,0)$ | $X$ |
| $(1,0)$ | $1$ |
| $(0,1)$ | $X^2+X$ |
| $(1,1)$ | $X^2$ |

The prover claims

$$
S=\sum_{(x,y)\in\{0,1\}^2}f(x,y)=1.
$$

Check the arithmetic:

$$
X+1+(X^2+X)+X^2=1,
$$

because each of $X$ and $X^2$ appears twice and cancels in characteristic $2$.

## 2. What sumcheck checks

For a two-variable polynomial of individual degree at most one, the prover sends the partial-sum polynomial

$$
g_1(t)=\sum_{y\in\{0,1\}} f(t,y).
$$

The verifier first checks $g_1(0)+g_1(1)=S$. It then samples a challenge $r_1\in\mathbb{F}_8$ and replaces the original claim with the smaller claim

$$
g_1(r_1)=\sum_{y\in\{0,1\}}f(r_1,y).
$$

In round two, the prover sends

$$
g_2(t)=f(r_1,t).
$$

The verifier checks $g_2(0)+g_2(1)=g_1(r_1)$, samples $r_2\in\mathbb{F}_8$, and is left with one evaluation claim:

$$
g_2(r_2)=f(r_1,r_2).
$$

In a larger proof system, the last value is checked against an authenticated polynomial evaluation, usually through a polynomial commitment opening. Here we can evaluate $f$ directly.

## 3. Round 1, fully calculated

First evaluate $f(t,y)$ at the two Boolean values of $y$:

$$
\begin{aligned}
f(t,0)&=X+(X+1)t,\\
f(t,1)&=X+(X+1)t+X^2+t\\
&=X^2+X+Xt.
\end{aligned}
$$

Therefore

$$
\begin{aligned}
g_1(t)&=f(t,0)+f(t,1)\\
&=\bigl(X+(X+1)t\bigr)+\bigl(X^2+X+Xt\bigr)\\
&=X^2+t.
\end{aligned}
$$

The verifier checks the initial sum:

$$
g_1(0)+g_1(1)=X^2+(X^2+1)=1=S.
$$

For this hand-worked transcript, fix the first challenge to

$$
r_1=X+1.
$$

In a real protocol, the verifier samples a fresh uniform field element after seeing the prover's round message. The chosen value here is for arithmetic we can follow by hand. The updated claim is

$$
s_1=g_1(r_1)=X^2+(X+1)=X^2+X+1.
$$

## 4. Round 2, fully calculated

Substitute $r_1=X+1$ into the first variable of $f$:

$$
\begin{aligned}
g_2(t)&=f(X+1,t)\\
&=X+(X+1)(X+1)+X^2t+(X+1)t.
\end{aligned}
$$

In $\mathbb{F}_8$,

$$
(X+1)^2=X^2+1,
$$

so both the constant and the coefficient of $t$ simplify to $X^2+X+1$:

$$
\begin{aligned}
g_2(t)&=\bigl(X+X^2+1\bigr)+\bigl(X^2+X+1\bigr)t\\
&=(X^2+X+1)(1+t).
\end{aligned}
$$

The verifier checks the round transition:

$$
g_2(0)+g_2(1)=(X^2+X+1)+0=s_1,
$$

because $g_2(1)=(X^2+X+1)+(X^2+X+1)=0$.

Fix the second challenge for this example to

$$
r_2=X^2.
$$

Then

$$
\begin{aligned}
s_2=g_2(r_2)
&=(X^2+X+1)+(X^2+X+1)X^2\\
&=(X^2+X+1)+1\\
&=X^2+X.
\end{aligned}
$$

The second line uses the inverse calculation from Lesson 1: $X^2(X^2+X+1)=1$.

## 5. Final verifier check

The verifier independently evaluates $f(r_1,r_2)$:

$$
\begin{aligned}
f(X+1,X^2)
&=X+(X+1)^2+X^4+(X+1)X^2\\
&=X+(X^2+1)+(X^2+X)+(X^2+X+1)\\
&=X^2+X.
\end{aligned}
$$

For the last product, $(X+1)X^2=X^3+X^2=(X+1)+X^2=X^2+X+1$. In the final sum, the three $X$ terms reduce to $X$, the three $X^2$ terms reduce to $X^2$, and the two constants cancel. This matches $s_2$, so every check in this honest transcript passes.

The protocol has reduced one sum over four Boolean inputs to a single evaluation at a random point. For $n$ variables, the same procedure takes $n$ rounds; each round fixes one more coordinate and sends a univariate polynomial.

## 6. Why a false claim is caught

The verifier must require each round polynomial to have the declared degree bound. If a false claim nevertheless passes the initial endpoint check, a dishonest prover can only arrange for the next claim to be wrong at a challenge root of a nonzero difference polynomial. A nonzero univariate polynomial of degree at most $d$ has at most $d$ roots. With independent uniform challenges, the standard sumcheck soundness error is at most

$$
\frac{d_1+d_2}{|\mathbb{F}_8|}.
$$

Here each individual degree is at most one, so this simple two-round example has error at most $2/8=1/4$. That is far too large for cryptographic use; real systems use much larger extension fields and account for all protocol and commitment soundness errors.

The arithmetic above shows **completeness** for an honest prover. Soundness additionally depends on degree checks, random challenges, and authenticating the final evaluation. Fiat–Shamir can derive challenges from a transcript in a noninteractive system, but it does not remove these algebraic checks.

## Exercises

1. Recompute $g_1(t)$ by first listing the two functions $f(t,0)$ and $f(t,1)$.
2. Repeat the protocol with $r_1=X$ and $r_2=X+1$. Show that both round transitions and the final evaluation agree.
3. Suppose a prover sends $g_1(t)=X^2+t+1$ instead. Does the initial endpoint check pass for the claimed sum $S=1$?
4. Explain why the final evaluation check would need a polynomial commitment opening if the verifier did not know $f$ explicitly.

## Further reading

The sumcheck protocol is a general algebraic tool. Flock's paper uses it as a subroutine in both zerocheck and lincheck; the next lesson specializes those checks. See [Flock, §2.3](https://arxiv.org/abs/2607.27491) for its sumcheck preliminaries.
