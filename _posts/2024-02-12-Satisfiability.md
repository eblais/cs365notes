---
title: 11. Satisfiability
author: Eric Blais
layout: post
---

The study of Boolean circuits is one way that we can hope to get more insights into the $$\mathbf{P}$$ versus $$\mathbf{NP}$$ problem.
It also gives a great way to approach the study of $$\mathbf{NP}$$-complete problems.


## Circuit Satisfiability

Consider the language

$$
\textsf{CircuitSAT} = \left\{ \left< C \right> : C \mbox{ is satisfiable}\right\}
$$

of all encodings of Boolean circuits that evaluate to True on at least one input.
We can show directly that this language is $$\mathbf{NP}$$-complete.

> **Theorem.**
> The language $$\textsf{CircuitSAT}$$ is $$\mathbf{NP}$$-complete.
{: .block-danger}

> **Proof.**
> We have already seen in the Non-Uniform Computation lecture that we can evaluate Boolean circuits efficiently with Turing machines.
> So there is polynomial-time verifier for $$\textsf{CircuitSAT}$$ that takes the (claimed) satisfying assignment as the certificate and simply simulates the encoded circuit $$C$$ on this input to verify that the circuit indeed evaluates to True.
> And so $$\textsf{CircuitSAT} \in \mathbf{NP}$$.
> 
> Consider now any language $$A \in \mathbf{NP}$$.
> Let $$V$$ be a polynomial-time verifier for $$A$$.
> As in the proof of Lemma 1 in the Non-Uniform Computation lecture, there is a polynomial-size circuit $$C$$ that can simulate $$V$$ on any input-certificate pair $$(x,c)$$ and evaluates to True if and only if $$V$$ accepts that pair.
> Furthermore, we can construct this circuit $$C$$ in polynomial time and hardcode the input $$x$$ in to obtain the circuit $$C'$$ that satisfies $$\left< C' \right> \in \textsf{CircuitSAT}$$ if and only if $$x \in A$$.
> Therefore, the construction gives a polynomial-time reduction from $$A$$ to $$\textsf{CircuitSAT}$$.
> Since this holds for all $$A \in \mathbf{NP}$$, by definition $$\textsf{CircuitSAT}$$ is $$\mathbf{NP}$$-hard.

As you may recall, we already established that another language, namely $$A_{TM}^{cert}$$ is $$\mathbf{NP}$$-complete.
But the significance in this result is that the $$\textsf{CircuitSAT}$$ result is the first _natural_ language that we have shown to be $$\mathbf{NP}$$-complete.
From there, we can easily complete the proof of the Cook-Levin theorem that $$\textsf{SAT}$$ is $$\mathbf{NP}$$-complete.



## Cook-Levin Theorem

The most natural satisfiability language is the language

$$
\textsf{SAT} = \{ \left< F \right> : F \mbox{ is a satisfiable formula}\}
$$

that considers the satisfiability of _formulas_ instead of Boolean circuits.
This language is also $$\mathbf{NP}$$-complete.

> **Cook-Levin Theorem.**
> $$\textsf{SAT}$$ is $$\mathbf{NP}$$-complete.
{: .block-danger}

> **Proof.**
> The languages $$\textsf{SAT}$$ is in $$\mathbf{NP}$$ because we can easily verify in polynomial time whether a given assignment $$x$$ satisfies a formula $$F$$ given the encoding of $$F$$.
> 
> We now want to prove that $$\textsf{SAT}$$ is $$\mathbf{NP}$$-hard.
> We have already shown that $$\textsf{CircuitSAT}$$ is $$\mathbf{NP}$$-hard, so it suffices to show that $$\textsf{CircuitSAT} \le_{\mathbf{P}} \textsf{SAT}$$.
> For any Boolean circuit $$C$$, we want to construct in polynomial time a CNF formula that is satisfiable if and only if the circuit $$C$$ is satisfiable.
> To do this, the formula $$F$$ that we construct will have one variable for each of the $$n$$ inputs to $$C$$ as well as one variable $$x_g$$ for each gate $$g$$ of $$C$$.
> We want to construct constraints that are satisfied if and only if the variables for the gates are assigned the correct value given the circuit's inputs.
> We can do this directly for the three types of gates.
> 
> A negation gate is simple.
> Let $$x_a$$ be the input to the negation gate associated to $$x_g$$. 
> Then the two constraints
> 
> $$
> (x_g \vee x_a) \wedge (\overline{x_g} \vee \overline{x_a})
> $$
> 
> are both satisfied if and only if $$x_g = \neg x_a$$.
> 
> When $$x_g$$ is the variable associated to the $$\wedge$$ gate $$g$$ that takes as inputs $$x_a,x_b,\ldots,x_z$$ then the constraints
> 
> $$
> (x_g \vee \overline{x_a} \vee \overline{x_b} \vee \cdots \vee \overline{x_z})
> \wedge
> (\overline{x_g} \vee x_a) \wedge (\overline{x_g} \vee x_b) \wedge \cdots \wedge (\overline{x_g} \vee x_z)
> $$
> 
> are all satisfied if and only if $$x_g = x_a \wedge x_b \wedge \cdots \wedge x_z$$.
> 
> Finally, when $$x_g$$ represents the $$\vee$$ gate with inputs $$x_a,\ldots,x_z$$ then
> 
> $$
> (\overline{x_g} \vee x_a \vee x_b \vee \cdots \vee x_z)
> \wedge
> (x_g \vee \overline{x_a}) \wedge (x_g \vee \overline{x_b}) \wedge \cdots \wedge (x_g \vee \overline{x_z})
> $$
> 
> are all satisfied if and only i f $$x_g = x_a \vee x_b \vee \cdots \vee x_z$$.
> 
> Combining the constraints for all the gates in $$C$$ and adding one additional constraint that consists simply of the variable for the ouput gate of $$C$$ gives us the final formula $$F$$.
> Its encoding can be constructed in polynomial time given the encoding of $$C$$.
> And $$F$$ is satisfiable if and only if $$C$$ is satisfiable, giving us the desired polynomial-time reduction from $$\textsf{CircuitSAT}$$ to $$\textsf{SAT}$$.


## 3SAT

We can show that an even more restricted variant of $$\textsf{SAT}$$ is also $$\mathbf{NP}$$-complete.

A _CNF formula_ (shorthand for a Boolean formula in Conjunctive Normal Form) is a Boolean formula with unbounded fan-in of depth 2, where the bottom-most gate is a single $$\wedge$$ gate, the inputs to that output gate are $$\vee$$ gates, and the inputs to each $$\vee$$ gates are subsets of the input variables or their negations.
Each $$\vee$$ gate in a CNF formula represents a _clause_.
The number of inputs to a $$\vee$$ gate is the _width_ of the corresponding clause.
The _size_ of a CNF formula is the number of clauses it contains, and the _width_ of a CNF formula is the maximum width of all of its clauses.

A natural language that is most useful in the study of $$\mathbf{NP}$$-completeness is the satisfiability of CNF formulas of width at most 3, denoted by

$$
\textsf{3SAT} = \{ \left< F \right> : F \mbox{ is a satisfiable CNF formula of width } \le 3\}.
$$


> **Theorem.**
> $$\textsf{3SAT}$$ is $$\mathbf{NP}$$-complete.
{: .block-danger}

> **Proof.**
> Looking back at the proof of the Cook-Levin theorem, we see that the polynomial-time reduction already converts general circuits into CNF formulas.
> The only missing step to modify the reduction so that it shows that $$\textsf{CircuitSAT} \le_{\mathbf{P}} \textsf{3SAT}$$ is to convert the resulting CNF formula into one that has width at most 3.
> 
> As it turns out, this final step is easy to accomplish.
> We can turn any clause $$x_1 \vee x_2 \vee x_3 \vee \cdots \vee x_k$$ of width $$k \ge 3$$ into a collection of clauses of width 3 by adding $$k-3$$ auxiliary variables $$y_1,\ldots,y_{k-3}$$ and taking the set of clauses
> 
> $$(x_1 \vee x_2 \vee y_1) \wedge (\overline{y_1} \vee x_3 \vee y_2) \wedge (\overline{y_2} \vee x_4 \vee y_3) \wedge \cdots \wedge (\overline{y_{k-3}} \vee x_{k-1} \vee x_k).$$
> 
> These clauses are all satisfied if and only if the original clause was satisfied, so we can convert the CNF formula $$\phi$$ produced by the reduction from $$\textsf{CircuitSAT}$$ to $$\textsf{SAT}$$ into one of width 3 that also satisfies the polynomial-time reduction conditions.


---

_Eric Blais &copy;2024 &mdash; Last edited on Feb. 12, 2024_
