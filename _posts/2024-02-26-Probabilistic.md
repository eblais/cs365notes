---
  title: 12. Randomized Computation
  author: Eric Blais
  date: 2024-02-26
  layout: post
---

Randomness is a powerful tool in the design and analysis of algorithms.
To understand it better, we introduce a formal model of randomized computation.


## Probabilistic Turing Machines

A _probabilistic Turing machine_ is  a variant of our standard Turing machine model in which we again add a different tape.
This is what we did previously, for example, in our definition of verifiers and Turing machines with advice.
In this case, however, the extra tape is a _randomness tape_ that we can imagine as being filled with random bits whenever we start execution of the machine.

> **Definition.**
> A _probabilistic Turing machine_ is a Turing machine that has a standard input tape as well as a read-only _randomness tape_. 
> The definition of a Turing machine includes a computable function $$\ell : \mathbb{N} \to \mathbb{N}$$ that determines the length of the random string generated for inputs of all lengths.
> The probabilistic Turing machine has independent tape heads for the input and randomness tapes, and each transition is determined by the current symbol under both tape heads.
{: .block-tip}

On input $$x$$ of length $$n$$, a probabilistic Turing machine's randomness tape is initialized with a string $$r \in \{0,1\}^{\ell(n)}$$ drawn uniformly at random from the binary strings of length $$\ell(n)$$.

The _time 
We could alternatively define a probabilistic Turing machine as a single-tape Turing machine that has the ability to flip coins during computational steps to decide between multiple possible transitions that it could follow.
This alternative definition is equivalent to the one we use; if it's useful, you can picture the probabilistic Turing machine as flipping the coins it plans to use during its computation ahead of time and writing the results on the randomness tape before starting its actual computation.

The main advantage of probabilistic Turing machines over deterministic Turing machines is that we can now meaningfully define what it means to have some _bounded-error_ computations that are correct most, but not all, of the time.

> **Definition.**
> Fix $$0 < \epsilon < \frac12$$.
> The probabilistic Turing machine $$M$$ _$$\epsilon$$-decides_ the language $$L_\epsilon(M)$$ when
> * $$M$$ always halts (on all inputs $$x$$ and on all random strings $$r$$).
> * For every input $$x \in L_\epsilon(M)$$, 
>
> $$\Pr_{r \in \{0,1\}^{\ell(|x|)}}[ M \mbox{ accepts } (x,r) ] \ge 1 - \epsilon.$$
> 
> * For every input $$x \notin L_\epsilon(M)$$,
> 
> $$\Pr_{r \in \{0,1\}^{\ell(|x|)}}[ M \mbox{ accepts } (x,r) ] \le \epsilon.$$
> 
{: .block-tip}


The class $$\mathbf{BPP}$$ is the set of all languages that can be $$\frac13$$-decided by polynomial-time probabilistic Turing machines.
The name of the class stands for $$\mathbf{B}$$ounded-error $$\mathbf{P}$$robabilistic $$\mathbf{P}$$olynomial-time, and in many ways it is the most natural extension of $$\mathbf{P}$$ for randomized computation.



## Primality Testing

Consider the language

$$
\textsf{PRIME} = \{ \left< N \right> : N \mbox{ is a prime number}\},
$$

where $$\left< N \right>$$ represents the binary encoding of a number and has length $$n = \lceil \log N \rceil$$.

Can you design an algorithm for testing if $$N$$ is a prime number?
The naïve exhaustive search algorithm that just checks whether $$m$$ divides $$N$$ for all $$1 < m < N$$ is correct, but it has exponential time complexity.

We can design a much faster randomized algorithm using some basic facts from number theory.
Say that a positive integer $$N$$ is _prime-like relative to_ the integer $$a \in \{1,2,\ldots,N-1\}$$ the two conditions

1. $$a^{N-1} \equiv 1 \pmod{N}$$ and
2. for all $$r \le s$$, if $$a^{2^r d} \equiv 1 \pmod{N}$$ then $$a^{2^{r-1}d} \equiv \pm 1 \pmod{N}$$

both hold, where in the second condition $$d$$ and $$s$$ are the only solution to the identity $$N-1 = d 2^s$$ for which $$d$$ is odd.
As the name suggests, the prime-like condition is always satisfied when $$N > 2$$ is a prime number.

> **Fact 1.**
> When $$N > 2$$ is any odd prime number, then $$N$$ is prime-like relative to every $$a \in \{1,2,\ldots,N-1\}$$.
{: .block-warning}

Odd numbers that are not primes, on the other hand, cannot be prime-like relative to many of the integers in $$\{1,2,\ldots,N-1\}$$.

> **Fact 2.**
> When $$N > 2$$ is any odd composite number, then $$N$$ is prime-like relative to at most $$\frac{N-1}4$$ of the integers $$a \in \{1,2,\ldots,N-1\}$$.
{: .block-warning}

These two facts let us design a very efficient randomized algorithm for $\frac13$-deciding $$\textsf{PRIME}$$.
This algorithm is known as the _Miller-Rabin primality test_.

> **Miller-Rabin Primality Test**($$N$$)
> 
> * If $$N = 2$$, _Accept_; else if $$N$$ is even _Reject_.
> * Draw $$a \in \{1,2,\ldots,N-1\}$$ uniformly at random.
> * If $$N$$ is prime-like relative to $$a$$ then _Accept_; otherwise _Reject_.

The correctness of the Miller-Rabin primality test follows directly from Facts 1 and 2.

> **Lemma.**
> The Miller-Rabin primality test $$\frac13$$-decides the $$\textsf{PRIME}$$ language.
{: .block-danger}

> **Proof.**
> First, we note that as in the deterministic setting, we will assume that polynomial-time randomized algorithms can be implemented by polynomial-time probabilistic Turing machines.
> This assumption is again justified by the Cobham-Edmonds thesis.
> 
> Next, we note that the Miller-Rabin test always halts, as it always just checks whether $$N$$ is prime-like relative to a single value of $$a$$, and once $$a$$ is fixed this check can be done with a deterministic algorithm that always halts.
> 
> Then we come to the two key parts of the proof of correctness:
> 
> * For any $$\left<N\right> \in \textsf{PRIME}$$, the integer $$N$$ is prime. 
> By Fact 1, the Miller-Rabin test always accepts since $$N$$ is prime-like to any $$a$$ chosen by the algorithm.
> 
> * For any $$\left< N \right> \notin \textsf{PRIME}$$, the integer $$N$$ is composite.
> If $$N$$ is even, then the Miller-Rabin test always correctly rejects.
> And if $$N$$ is an odd composite number, then by Fact 2, the probability that the Miller-Rabin test incorrectly accepts is
> 
> $$\Pr_{a \in \{1,2,\ldots,N-1\}}[ N \mbox{ is prime-like relative to } a ] \le \frac{(N-1)/4}{N-1} = \frac14.$$

Though we will not prove this here, the Miller-Rabin Primality Test can be implemented to have time cost $$O(\log^3 N) = O(n^3)$$ that is polynomial in the input size. 
Therefore, $$\textsf{PRIME} \in \mathbf{BPP}$$.



## Success Amplification

The Miller-Rabin primality test is not just interesting from a theoretical point of view; it is also commonly used in practice.
But in practice, we usually want the probability of observing a random error to be much less than $$\frac13$$ or $$\frac14$$.
And, luckily, it is very easy to reduce the error of this algorithm dramatically: simply run it multiple times, and _reject_ $$\left< N \right>$$ if any of the instances of the Miller-Rabin test reject.

When $$N$$ is prime, as we have seen in the analysis, the algorithm always correctly accepts. 
And when $$N$$ is a composite number, each call to the Miller-Rabin test draws a number $$a \in \{1,2,\ldots,N-1\}$$ independently from the numbers chosen previously.
So the probabilities that each instance incorrectly accept are _independent_, and in general the probability that $$k$$ calls to the Miller-Rabin test with an odd composite number $$N$$ all incorrectly accept is at most

$$
\begin{align*}
\Pr_{a_1,a_2,\ldots,a_k}[ N &\mbox{ is prime-like relative to all of } a_1, a_2, \ldots, a_k] \\
&= \left( \Pr_a[ N \mbox{ is prime-like relative to } a] \right)^k \le \frac1{4^k}.
\end{align*}
$$

So, for example, running the Miller-Rabin test $$k=10$$ times results in an error probability of less than 1 in $$1,000,000$$.

This ability to greatly reduce the error of randomized algorithms is not specific to the Miller-Rabin primality test.
It is in fact one of the most powerful features of randomized algorithms, a feature usually called efficient _success amplification_.
With this technique, we can show that every language in $$\mathbf{BPP}$$ admits efficient success amplification.

> **Theorem.**
> For every language $$L \in \mathbf{BPP}$$ and any two constants $$c_0,c_1 \ge 0$$, there is a polynomial-time probabilistic Turing machine that $$\epsilon$$-decides $$L$$ with $$\epsilon = e^{- c_0 n^{c_1}}$$.
{: .block-danger}

To prove the Theorem, we use a special case of a most powerful inequality in probability theory.

> **Chernoff bound / Hoeffding inequality.** (Special case)
> Let $$X_1,X_2,\ldots,X_k$$ be independent random variables that each take the values $$0$$ or $$1$$.
> Define $$T = \frac1k \sum_{i=1}^k X_i$$ to be their average value.
> Then for any $$t > 0$$,
> 
> $$
> \Pr[ T - \mathrm{E}[T] \ge t ] \le e^{-2t^2 k}.
> $$
{: .block-warning}

Recall that by linearity of expectation, for the value $$T$$ defined in the Chernoff bound,

$$
\mathrm{E}[T] 
= \mathrm{E}\left[\frac1k \sum_{i=1}^k X_i\right] 
= \frac1k \sum_{i=1}^k \mathrm{E}[X_i]
= \frac1k \sum_{i=1}^k \Pr[ X_i = 1 ].
$$
is the expected fraction of the variables $$X_i$$ that take the value $$1$$.

We're now ready to complete the proof of our success amplification theorem.

> **Proof.**
> Let $$A$$ be a polynomial-time probbabilistic Turing machine that $$\frac13$$-decides $$L$$.
> Define $$B$$ to be the algorithm that on an input $$x$$ of length $$n$$ runs $$k = 18 c_0 n^{c_1}$$ copies of $$A$$ on $$x$$ and accepts if and only if more than half of the instances of $$A$$ accept.
> The algorithm $$B$$ always halts, and it also runs in polynomial time.
> 
> Fix now any $$x \in L$$.
> For $$i=1,2,\ldots,k$$, let $$X_i$$ be a random variable that takes the value $$1$$ if the $$i$$th instance of $$A$$ incorrectly rejects $$x$$, and $$X_i = 0$$ otherwise.
> Then $$T = \frac1k \sum_{i=1}^k X_i$$ represents the fraction of instances of $$A$$ that incorrectly reject $$x$$ when we run $$B$$.
> Its expected value is bounded above by 
> 
> $$
> \mathrm{E}[T] 
> = \frac1k \sum_{i=1}^k \Pr[ X_i = 1] 
> = \frac1k \sum_{i=1}^k \Pr[ A \mbox{ rejects } x ]
> \le \frac1k \sum_{i=1}^k \frac13 
> = \frac13.
> $$
> 
> Since $$B$$ incorrectly rejects if and only if $$T \ge \frac12$$, we have
> 
> $$
> \Pr[ B \mbox{ rejects } x ] 
> = \Pr\left[ T \ge \frac12 \right] 
> = \Pr\left[ T - \mathrm{E}[T] \ge \frac12 - \mathrm{E}[T]\right] 
> \le \Pr\left[ T - \mathrm{E}[T] \ge \frac16\right].
> $$
> 
> The variables $$X_1,\ldots,X_k$$ are independent, so we can apply Hoeffding's inequality to obtain
> 
> $$
> \Pr[ B \mbox{ rejects } x] 
> \le \Pr\left[ T - \mathrm{E}[T] \ge \frac16 \right] 
> \le e^{-2(1/6)^2k} = e^{-c_0 n^{c_1}}.
> $$
> 
> Finally, for any $$x \notin L$$, we can repeat the exact same argument but defining $$X_i$$ to take the value $$1$$ when $$A$$ incorrectly accepts $$x$$ and $$0$$ otherwise. 
> Doing so shows that the probability that $$B$$ incorrectly accepts any such $$x$$ is again at most $$e^{-c_0 n^{c_1}}$$.

Note that the success amplification theorem is extremely strong: even the special case where $$c_1 = 0$$ shows that if we can decide a language $$L$$ in polynomial time with error $$\frac13$$, then we can also decide it with error $$\frac1{100}$$ or $$\frac1{1,000,000}$$ or any other small error probability of our choosing.

In fact, another important consequence of success amplification is that the choice of $$\frac13$$ is not important in the definition of the class $$\mathbf{BPP}$$: changing the value $$\frac13$$ to any other constant in the range $$0 < \epsilon < \frac12$$ in the definition of the class does not change the set of languages that are in $$\mathbf{BPP}$$.



---

_Eric Blais &copy;2024 &mdash; Last edited on Feb. 28, 2024_

