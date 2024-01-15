---
  title: 4. Undecidability
  author: Eric Blais
  layout: post
  date: 2024-01-12
  cover: assets/images/undecidability.jpg
---

We saw in the last lecture that the language $$A_{TM} = \{ \left< M \right>x : M \mbox{ accepts } x\}$$ is undecidable.
This is only one example of a fundamental limitation of Turing machines:

_Turing machines cannot predict what other Turing machines will do without simulating them._

This is a theme that will reappear again later in the complexity part of the course. 
But for now, let's see exactly what is meant by this statement in the context of decidability.


## More Undecidable Languages

The proof of the undecidability of $$A_{TM}$$ can easily be modified to show that the halting problem is undecidable.

> **Theorem 1.**
> The language
> 
> $$
> \textsf{Halt}_{TM} = \{ \left< M \right>x : M \mbox{ halts on input } x \}
> $$
> 
> is undecidable.
{: .block-danger}

> **Proof.**
> Assume on the contrary that $$\textsf{Halt}_{TM}$$ is decidable by the Turing machine $$T$$.
> Consider the machine $$M$$ that on input $$x$$ does the following:
>
> 1. Obtain its own encoding $$\left< M\right>$$ using the Recursion Theorem.
> 2. Run $$T$$ on input $$\left< M \right> x$$.
> 3. If $$T$$ accepts, run forever in an infinite loop; otherwise, halt and accept.
> 
> By construction, $$M$$ halts on $$x$$ if and only if $$T$$ does not accept $$\left< M \right> x$$.
> But this contradicts the claim that $$T$$ decides $$\textsf{Halt}_{TM}$$.

We can also modify the proof to show that determining if a Turing machine accepts _any_ string is also undecidable.
Define $$L(M) = \{ x \in \{0,1\}^* : M \mbox{ accepts } x\}$$; recall that $$L(M)$$ denotes the language _recognized_ by $$M$$.

> **Theorem 2.**
> The language
> 
> $$
> \textsf{Empty}_{TM} = \{ \left< M \right> : L(M) = \emptyset \}
> $$
> 
> is undecidable.
{: .block-danger}

> **Proof.**
> Assume on the contrary that $$\textsf{Empty}_{TM}$$ is decidable by the Turing machine $$T$$.
> Consider the machine $$M$$ that on input $$x$$ does the following:
>
> 1. Obtain its own encoding $$\left< M\right>$$ using the Recursion Theorem.
> 2. Run $$T$$ on input $$\left< M \right>$$.
> 3. If $$T$$ accepts, reject; otherwise accept.
> 
> By this construction, $$L(M) = \{0,1\}^*$$ when $$T$$ accepts $$\left< M \right>$$ and $$L(M) = \emptyset$$ when $$T$$ rejects.
> This contradicts the claim that $$T$$ decides $$\textsf{Empty}_{TM}$$.

We can extend this theorem to show that it is impossible to decide _any_ non-trivial property of languages of Turing machines.


> **Rice's Theorem.**
> Let $P$ be a subset of all languages over $$\{0,1\}^n$$ such that
> 
> 1. There exists a Turing machine $$M_1$$ for which $$L(M_1) \in P$$, and
> 2. There exists a Turing machine $$M_2$$ for which $$L(M_2) \notin P$$.
> 
> Then the language
> 
> $$
> \textsf{L}_{P} = \{ \left< M \right> : L(M) \in P \}
> $$
> 
> is undecidable.
{: .block-danger}

> **Proof.**
> Assume on the contrary that $$L_P$$ is decidable by the Turing machine $$T$$.
> 
> Consider the machine $$M$$ that on input $$x$$ does the following:
>
> 1. Obtain its own encoding $$\left< M\right>$$ using the Recursion Theorem.
> 2. Run $$T$$ on input $$\left< M \right>$$.
> 3. If $$T$$ accepts, simulate $$M_2$$ on $$x$$; otherwise simulate $$M_1$$ on $$x$$.
> 
> By this construction, $$L(M) = L(M_2) \notin P$$ when $$T$$ accepts $$\left< M \right>$$ and $$L(M) = L(M_1) \in P$$ when $$T$$ rejects.
> This contradicts the claim that $$T$$ decides $$L_P$$.

Rice's Theorem immediately implies Theorem 2 because $$P = \{\emptyset\}$$ is a non-trivial property of languages of Turing machines.
It also implies that we can't decide if the language recognized by a Turing machine includes the empty string, is finite or infinite, contains an even number of strings of length at most 100, is a regular language, etc.
But note that it does _not_ imply that we can't decide properties of Turing machines themselves; given a description of a Turing machine, we can certainly decide whether it has an even number of states, for example.

(Also, note that the undecidability of $$A_{TM}$$ and $$\mathsf{Halt}_{TM}$$ do not immediately follow from Rice's Theorem because the languages $$A_{TM}$$ and $$\mathsf{Halt}_{TM}$$ are not of the right form.)


## Reductions

We have seen two different types of arguments that can be used to prove the undecidability of a language: the Recursion Theorem method, and diagonalization arguments.
There is a third method: _reduction_ arguments.
Informally, if we show that being able to decide language $$L$$ implies that we can decide another language that is already known to be undecidable, like $$A_{TM}$$, then this proves that $$L$$ must also be undecidable.

Here is a simple example of a proof by reduction.

> **Theorem 3.**
> The language
> 
> $$
> A_{TM}^{\varepsilon} = \{ \left< M \right> : M \mbox{ accepts } \varepsilon \}
> $$
> 
> is undecidable.
{: .block-danger}

> **Proof.**
> Assume on the contrary that the Turing machine $$T$$ decides $$A_{TM}^\varepsilon$$.
> 
> Let us now define a Turing machine $$A$$ that takes input $$\left< M \right> x$$.
> Let $$M'$$ be a Turing machine that first writes $$x$$ on the tape and then copies the behaviour of $$M$$. 
> From the encoding of $$M$$ and the string $$x$$, $$A$$ can determine the encoding of $$M'$$.
> So it can call $$T$$ on $$\left< M' \right>$$ to determine whether $$M'$$ accepts $$\varepsilon$$ or not.
> 
> And since $$M'$$ accepts $$\varepsilon$$ if and only if $$M$$ accepts $$x$$, then $$A$$ decides the language $$A_{TM}$$, a contradiction.

Note that Theorem 3 also follows directly from Rice's Theorem.
Here's another example that is a bit more interesting.

The _$$k$$th Busy beaver number_ is the maximum number $$BB_k$$ of steps that a Turing machine with $$k$$ states can complete before halting on a tape that is initially empty.
(Here the maximum is taken over all Turing machines with $$k$$ states that _do_ halt after a finite number of steps when the input is $$\varepsilon$$.)
The Busy beaver numbers grow incredibly fast as a function of $$k$$.
And they are uncomputable in the following sense.

> **Theorem 4.**
> The language
> 
> $$
> B = \{ \left< k \right> \left< n \right> : BB_k \le n \}
> $$
> 
> is undecidable.
{: .block-danger}

> **Proof.**
> Assume on the contrary that there is a Turing machine $$T$$ that decides the language $$B$$.
> 
> Let us now define a Turing machine $$A$$ that takes input $$\left< M \right> x$$.
> As a first step, $$A$$ determines the number $$k$$ of states in $$M$$. 
> (This is easily done from the encoding of $$M$$.)
> Then by calling $$T$$ with input $$\left< k \right> \left< n \right>$$ for $$n=1,2,3,\ldots$$
> until $$T$$ accepts, $$A$$ can determine the value of $$BB_k$$.
> (Note that since there are only finitely many distinct Turing machines with $$k$$ states, the value of $$BB_k$$ is finite and so $$T$$ will accept after a finite number of calls.)
> 
> Now $$A$$ can simulate up to $$BB_k$$ steps of computation of $$M$$ on input $$\varepsilon$$.
> Specifically, it can do that by copying the behaviour of the Universal Turing Machine with one additional twist: a counter that is incremented after each simulation step and that interrupts the simulation when it reaches the value $$BB_k$$.
> If $$M$$ accepts or rejects during the simulation, $$A$$ does the same.
> Otherwise, at the end of $$BB_k$$ steps of simulation, $$A$$ halts and rejects.
> 
> Then $$A$$ decides the language $$A_{TM}^{\epsilon}$$.
> That's because if $$M$$ accepts or rejects $$\varepsilon$$, then $$A$$ does the same.
> And if $$M$$ runs for more than $$BB_k$$ steps, then by definition of the Busy beaver numbers it must run forever, which means that it does not accept $$\varepsilon$$.


## Recognizability

We have introduced two different notions of computability by Turing machines.
The first, and the one that corresponds most naturally the intuitive notion of "effective computability" is _decidability_, and requires the Turing machine to halt on all inputs.
The relaxation of this notion, called _recognizability_, requires a Turing machine to accept all the strings in the language, but allows them to either reject or run forever on inputs that are not in the language.
We say that a language is _recognizable_ if there is a Turing machine that recognizes it.

Every language that is decidable is obviously also recognizable.
But the converse statement is false.

> **Proposition 5.**
> The undecidable language $$A_{TM}$$ is recognizable.
{: .block-danger}

> **Proof.**
> Consider the Universal Turing Machine $$U$$. 
> On input $$\left< M \right> x$$, it simulates $$M$$ on $$x$$ and, in particular, accepts if and only if $$M$$ accepts $$x$$.
> Therefore, $$U$$ recognizes $$A_{TM}$$.

What about the complement 

$$
\overline{A_{TM}} = \{0,1\}^* \setminus A_{TM} = \{ \left<M \right>x : M \mbox{ does not accept } x \}?
$$

We can show that this language is unrecognizable using the following general result.

> **Theorem 6.**
> If a language $$L$$ and its complement $$\overline{L} = \{0,1\}^* \setminus L$$ are both recognizable, then $$L$$ and $$\overline{L}$$ are both decidable.
{: .block-danger}

> **Proof.**
> Assume that $$T_1$$ and $$T_2$$ recognize $$L$$ and $$\overline{L}$$, respectively.
> 
> Let $$M$$ be a Turing machine that simulates both $$T_1$$ and $$T_2$$ in parallel.
> Specifically, it dedicates separate portions of the tape for the simulation of both $$T_1$$ and $$T_2$$ and interleaves their simulations by performing one step of computation of each of them at a time.
> The simulation is completed when either $$T_1$$ or $$T_2$$ accepts.
> If $$T_1$$ is the machine that accepts, $$M$$ also accepts.
> Otherwise, if $$T_2$$ accepts then $$M$$ rejects.
> 
> When $$x \in L$$, then $$T_1$$ is guaranteed to accept $$x$$ after a finite number of steps, and $$T_2$$ is guaranteed not to accept, so $$M$$ correctly accepts $$x$$.
> Similarly, when $$x \in \overline{L}$$ then $$T_2$$ is guaranteed to accept after a finite number of steps and $$T_1$$ will not accept so $$M$$ correctly rejects $$x$$.
> Therefore, $$M$$ decides $$L$$, and the machine $$M'$$ obtained by switching the accept and reject labels in $$M$$ decides $$\overline{L}$$.


> **Corollary.**
> The language $$\overline{A_{TM}}$$ is unrecognizable.
{: .block-danger}

> **Proof.**
> We have already seen that $$A_{TM}$$ is recognizable.
> If $$\overline{A_{TM}}$$ was also recognizable, then by Theorem 6 the language $$A_{TM}$$ would be decidable, which we already know is false.



---

_Eric Blais &copy;2024 &mdash; Last edited on Jan. 14, 2024_

