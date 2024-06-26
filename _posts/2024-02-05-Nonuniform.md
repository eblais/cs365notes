---
title: 9. Non-Uniform Computation
author: Eric Blais
date: 2024-02-01
layout: post
---

We saw in the last lecture that Boolean circuits provide a model of computation that is very different from Turing machines.
But the two models are also closely related to each other, as we will see in this lecture.


## Circuits Simulate TMs

The starting point for the connection between Boolean circuits and Turing machines is the following
fundamental lemma showing that Turing machines can be efficiently simulated with Boolean circuits.

> **Lemma 1.** 
> For any single-tape Turing machine $$M$$ that runs in time $$t$$ and uses at most $$s$$ squares of tape on any input of length $$n$$, there is a circuit $$C$$ of size $$O(s t)$$ that outputs $$1$$ on input $$x \in \{0,1\}^n$$ if and only if $$M$$ accepts $$x$$.
{: .block-danger}

> **Proof.**
> We first observe that any configuration of the machine $$M$$ can be represented using a binary string.
> Namely, for each of the $$s$$ squares of the tape that $$M$$ might use, we encode two pieces of information using binary strings: the _state_ of the machine is stored by encoding the index of the current state of $$M$$ if the tape head is over the current square, and a special code (say, $$0$$) is encoded in the state instead if the tape head is over a different square.
> And the _symbol_ on the current square is stored by using the binary encoding of the index of the symbol currently written on that tape square.
> 
> By writing the intial configuration of $$M$$ on input $$x$$ and then writing the configurations yielded by this initial configuration one below the other, we obtain a _tableau_ representation of the execution of $$M$$ on input $$x$$.
> The machine $$M$$ accepts $$x$$ if and only if any of the configurations in this tableau is an accepting configuration.
>
> We can build each row of the tableau for $$M$$ given the previous row using a circuit.
> Specifically, we create one circuit for each bit of the current row we are building.
> The input to each of these circuits is a subset of the bits of the previous row.
> And the key observation is that each of these circuits only depends on a _constant_ number of bits of the previous row since the state and symbol for the $$i$$th square in the tape only ever depend on the contents of the squares $$i-1$$, $$i$$, and $$i+1$$ in the previous configuration.
> As a result, each of these circuits can be implemented with only $$O(1)$$ gates, and the total size of the circuit required to build the entire tableau is $$O(s \cdot t)$$.
> 
> We then obtain $$C$$ by taking the circuit that generates the tableau of $$M$$ with the input $$x$$ as the only non-fixed inputs to the first configuration, and adding another $$O(s \cdot t)$$ gates to output $$1$$ if and only if any of the configurations in the tableau is accepting.

Since a Turing machine with polynomial time complexity can only use a polynomial number of squares on the tape, Lemma 1 implies the following important consequence.

> **Theorem 2.**
> Every language in $$\mathbf{P}$$ can be computed with a family of polynomial-size circuits.
{: .block-danger}

The converse to this statement is not true in general: there are languages that can be computed by families of polynomial-size circuits but are not in $$\mathbf{P}$$.
In fact, the converse statement fails in a much more dramatic way: there are languages that can be computed by families of _constant_-sized circuits which are not even decidable.
However, we can consider a _non-uniform_ variant of Turing machines that can efficiently simulate circuits. 


## The Class P/poly

The notion of non-uniform Turing machines is obtained via the following variant of our usual Turing machine model.

> **Definition.**
> A _Turing machine with advice_ is a Turing machine that in addition to its usual tape also has a special _advice tape_ and is defined with a sequence of _advice strings_ $$a_0, a_1, a_2, \ldots$$, one for each $$n \in \mathbb{N}$$.
> When running a Turing machine with advice on input $$x \in \{0,1\}^n$$, the machine is initialized with the advice string $$a_n$$ corresponding to the length of $$x$$ on the advice tape.
{: .block-tip}

The execution of the Turing machine with advice is the same as for standard Turing machines.
We will be interested in _polynomial-time Turing machines with advice_.
Since time cost is again just measured in terms of the input string itself (and not the advice string), this means that we can assume without loss of generality that the advice strings all have polynomial length.

> **Definition.**
> The language class $$\mathbf{P/poly}$$ represents the set of languages that can be decided by polynomial-time Turing machines with advice strings that have length polynomial in $$n$$.
{: .block-tip}

We can also consider other classes of languages obtained by any other restriction on the length of the advice strings. 
For instance, $$\mathbf{P/1}$$ represents the set of languages that can be computed by polynomial-time Turing machines with a single bit of advice.

The class $$\mathbf{P/poly}$$ exactly captures the set of languages that can be computed by polynomial-sized circuits.

> **Theorem 3.**
> The language class $$\mathbf{P/poly}$$ is exactly the set of languages that can be computed by families of polynomial-size Boolean circuits.
{: .block-danger}

> **Proof.**
> The proof of Theorem 1 can again be extended slightly (by hardcoding the advice bits in the intial configuration) to show that every language in $$\mathbf{P/poly}$$ can be computed by a family of polynomial-size circuits.
> 
> For the other direction, consider the Turing machine with advice $$U$$ that interprets its advice string as the encoding of a Boolean circuit, and then simulates the circuit on its input $$x$$.
> Every Boolean circuit of polynomial size can be encoded with a binary string of polynomial length.
> And it can be simulated in polynomial time by sequentially computing the output to each gate in the circuit.
> Therefore, if $$L$$ can be computed by a family of polynomial-size circuits, the machine $$U$$ with advice strings corresponding to the circuits in this family correctly decides $$L$$ in polynomial time, so $$L \in \mathbf{P/poly}$$.



---

_Eric Blais &copy;2024 &mdash; Last edited on Feb. 12, 2024_

