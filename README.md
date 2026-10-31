# binary-fields.research

Research mathematical primitives to understand binary-field-based zero-knowledge proof systems such as Binius and Flock.

## Lessons

1. [Arithmetic in GF(2³)](math/lesson-1.md) — construct $\mathbb{F}_8$ and work through addition, multiplication, inverses, and a Boolean-hypercube example.
2. [Multilinear interpolation on the Boolean hypercube](math/lesson-2.md) — derive the multilinear extension from its Boolean values and prove uniqueness.
3. [A complete sumcheck protocol over GF(2³)](math/lesson-3.md) — follow both prover messages, verifier checks, challenges, and the final evaluation by hand.
4. [Zerocheck and lincheck](math/lesson-4.md) — derive randomized constraint checking and connect the protocols to Flock's R1CS architecture.

The next lessons will cover polynomial commitments, proximity testing, and proof composition.
