---
  title: 18. Nonregular Languages
  author: Eric Blais
  date: 2024-03-19
  layout: post
---

One of the interesting properties of the class of $$\mathbf{REGULAR}$$ languages is that we have good tools for determining when languages are _not_ in the class. 

## Pumping Lemma

We saw in the last lecture that DFAs (or _deterministic finite automata_) can be defined as Turing machines with a read-only input tape that can only be traversed left-to-right and no work tape.
It can also be defined more directly as an abstract machine that can be described using a _transition diagram_ like this one:

![DFA transition diagram](assets/images/DFA.png)

More precisely, a DFA $$M$$ is defined by:

* a set $$Q = \{1,2,\ldots,m\}$$ of _states_;
* a subset $F \subseteq Q$ of these states that are the _accepting states_; and
* a _transition function_ $$\delta : Q \times \{0,1\} \to Q$$ that indicate which state is reached from a given state when the next symbol of the input string read by $$M$$ is the one that labels the edge.

The _start state_ of $$M$$ is by convention the first state $$1$$. 
The DFA $$M$$ _accepts_ a string $$x \in \{0,1\}^*$$ if and only if it ends up in one of the accepting states in $$F$$ after reading all of the symbols of $$x$$.

This transition diagram view of DFAs lead to a simple observation: when the DFA $$M$$ has $$m$$ states, then every string $$x$$ of length $$|x| > m$$ is associated with a walk on the transition diagram of $$M$$ that must visit some state more than once.
In other words, the walk must include a _cycle_.
This simple observation may seem almost trivial, but it implies that all regular languages have a useful _pumping property_.

> **Pumping Lemma.**
> For every regular language $$L$$, there is a number $$p$$ such that for any string $$s \in L$$ of length at least $$p$$, we can write $$s = xyz$$ where
> + $$\lvert y \rvert > 0$$, 
> + $$\lvert xy \rvert \le p$$, and
> + For each $$k \ge 0$$, $$x y^k z \in L$$.
{: .block-danger}

> **Proof.**
> Let $$M = (m, \delta, F)$$ be a DFA that recognizes $$L$$. 
> We will show that $$L$$ satisfies the pumping property for the value $$p = m$$.
> 
> Fix any string $$s$$ of length $$|s| \ge m$$ that is accepted by $$M$$. 
> Let $$q_0, q_1, \ldots, q_p \in [m]$$ denote the states of $$M$$ reached after reading the first $$i$$ symbols of $$s$$ for $$i = 0, 1, \ldots, p$$.
> 
> Since $$M$$ has only $$m$$ distinct states, by the pigeonhole principle, there must be two indices $$0 \le i < j \le m$$ for which $$q_i = q_j$$. 
> Define $$x = s_1\cdots s_i$$, $$y = s_{i+1}\cdots s_j$$, and $$z = s_{j+1} \cdots s_{|s|}$$, so that $$s = xyz$$.
> 
> By construction, $$s = xyz$$. Since $$j > i$$, the string $$y$$ has length at least 1. 
> And since $$j \le m$$, $$|xy| \le m = p$$. 
> Finally, we note that the string $$y$$ induces a path from $$q_i$$ back to itself, so for any $$k \ge 0$$ the string $$xy^k$$ ends up at state $$q_i$$ in $$M$$. 
> So $$xy^kz$$ is accepted by $$M$$.

A language $$L$$ that satisfies the condition of the lemma is said to satisfy the *pumping property*. 

A value $$p$$ for which $$L$$ satisfies the pumping property is known as a *pumping length* of $$L$$.

## Using the Pumping Lemma

When we use the Pumping Lemma to show that a given language is not regular, we usually want to use its *contrapositive form*:

> **Pumping Lemma.** (Contrapositive form)
> Let $$L$$ be a language that satisfies the following property:
> For every positive integer $$p$$, 
> there exists a string $$s \in L$$ of length $$\lvert s\rvert \ge p$$
such that 
> for every decomposition $$s=xyz$$ with $$|y| > 0$$ and $$|xy| \le p$$, 
> there exists a value $$i \ge 0$$ where $$xy^iz \notin L$$.
> Then $$L$$ is not a regular language.
{: .block-danger}

The Pumping Lemma can be used to show that even some very simple languages are not regular.

> **Proposition.**
> The language $$L = \{ 0^n 1^n : n \ge 0 \}$$ is not regular.
{: .block-danger}

> **Proof.**
> For any $$p \ge 1$$, choose $$s = 0^p1^p$$. 
> This string is in $$L$$ and has length $$|s| \ge p$$.
>
> Fix any decomposition $$s = xyz$$ with $$|xy| \le p$$ and $$|y| > 0$$. 
> In any such decomposition, $$y = 0^\ell$$ for some $$\ell \ge 1$$. 
> But then $$xy^2z = 0^{p+\ell}1^p \notin L$$.
> 
> Hence, by the (contrapositive form of the) pumping lemma, $$L$$ is not regular.

The language above can be thought of as an "equality" language, in that it contains the strings of the form $0^\*1^\*$ with an equal number of 0s and 1s. 
The analogous "less than" and "greater than" languages are also not regular.

> **Proposition.**
> The language $$L_< = \{ 0^m 1^n : n > m \ge 0 \}$$ is not regular.
{: .block-danger}

> **Proof.**
> For any $$p \ge 1$$, choose $$s = 0^p1^{p+1}$$. 
> This string is in $$L$$ and has length $$|s| \ge p$$.
> 
> Fix any decomposition $$s = xyz$$ with $$|xy| \le p$$ and $$|y| > 0$$. 
> In any such decomposition, $$y = 0^\ell$$ for some $$\ell \ge 1$$. 
> But then $$xy^2z = 0^{p+\ell}1^{p+1} \notin L$$.
> 
> Hence, by the pumping lemma, $$L$$ is not regular.


> **Proposition.**
> The language $$L_> = \{ 0^m 1^n : m > n \ge 0 \}$$ is not regular.
{: .block-danger}

> **Proof.**
> For any $$p \ge 1$$, choose $$s = 0^{p+1}1^p$$. 
> This string is in $$L$$ and has length $$|s| \ge p$$.
> 
> Fix any decomposition $$s = xyz$$ with $$|xy| \le p$$ and $$|y| > 0$$. 
> In any such decomposition, $$y = 0^\ell$$ for some $$\ell \ge 1$$. 
> But then $$xy^0z = xz = 0^{p+1-\ell}1^{p} \notin L$$.
> 
> Hence, by the pumping lemma, $$L$$ is not regular.

The Pumping Lemma can also be used to show that some simple unary languages are not regular.

> **Proposition.**
> The language $$L = \{ 0^{2^n} : n \ge 0 \}$$ is not regular.
{: .block-danger}

> **Proof.**
> For any $$p \ge 1$$, choose $$s = 0^{2^p}$$. 
> This string is in $$L$$ and has length $$|s| \ge p$$.
> 
> Fix any decomposition $$s = xyz$$ with $$|xy| \le p$$ and $$|y| > 0$$. 
> In any such decomposition, $$y = 0^\ell$$ for some $$\ell$$ in the range $$1, \ldots, p$$. 
> But then $$xy^2z = 0^{2^p + \ell}$$ is a string of length between $$2^p + 1 > 2^p$$ and $$2^p + p < 2^p + 2^p = 2^{p+1}$$, so $$xy^2z \notin L$$.
> 
> Hence, by the pumping lemma, $$L$$ is not regular.


## Myhill-Nerode Theorem

The pumping lemma is a useful tool for showing that some languages are non-regular.
But one important warning about it is that the converse of the lemma is not true: there are non-regular languages that satisfy the pumping property.
So it does not _characterize_ the set of regular languages.

There is, however, a stronger result that does characterize the class of regular languages.
To introduce this tool, we first need a new definition.

> **Definition.**
> The strings $$x,y \in \{0,1\}^*$$ are _$$L$$-equivalent_ for some language $$L \subseteq \{0,1\}^*$$, denoted $$x \equiv_L y$$, if for every string $$z \in \{0,1\}^*$$, we have that $$xz \in L \Leftrightarrow yz \in L$$.
{: .block-tip}

(As you can verify directly, the relation $$\equiv_L$$ does define an equivalence relation, thus justifying the name and symbol we use for it.)

The _index_ of a language $$L \subseteq \{0,1\}^*$$ is the minimum cardinality of a set $$X \subseteq \{0,1\}^*$$ such that every string $$y \in \{0,1\}^*$$ is $$L$$-equivalent to some string $$x \in X$$.

As it turns out, the index of a language provides a characterization of regular languages, in the following sense.

> **Myhill-Nerode Theorem.**
> The language $$L \subseteq \{0,1\}^*$$ is regular if and only if it has finite index.
{: .block-danger}

> **Proof.**
> $$(\Rightarrow)$$ 
> Let $$L$$ be any regular language.
> Let $$M$$ be a DFA with $$m$$ states that decides $$L$$.
> Without loss of generality, assume that each state of $$M$$ is reachable by some string.
> Form $$X$$ by taking one string that causes $$M$$ to halt at each of its states.
> Then every string $$y \in \{0,1\}^*$$ is $$L$$-equivalent to the string $$x \in X$$ associated with the state in which $$M$$ halts on input $$y$$, since $$M$$ then halts on the same state in inputs $$xz$$ and $$yz$$ for any string $$z \in \{0,1\}^*$$.
> 
> $$(\Leftarrow)$$
> Let $$X = \{x_1,\ldots,x_m\}$$ be a set of minimal cardinality that defines the index of $$L$$.
> We can create a DFA $$M$$ by making one state for each of these strings and defining the transition $$\delta(x_i,\sigma)$$ to be the state for the string $$x_j$$ that satisfies $$x_j \equiv_L x_i \sigma$$ for each $$\sigma \in \{0,1\}$$.
> Set the accepting states of $$M$$ to be the states corresponding to the strings in $$X \cap L$$.
> Then $$M$$ decides $$L$$ (exercise: verify this claim!) and so $$L$$ is a regular language.

Note that the proof established even more than what was required by the statement of the Myhill-Nerode Theorem: it shows that the index of a regular language $$L$$ determines the _state complexity_ of $$L$$ &mdash; the minimum number of states of any DFA that decides it.

-----

_Eric Blais &copy;2024 &mdash; Last edited on Mar. 19, 2024_
