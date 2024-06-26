---
  title: 19. Communication Complexity
  author: Eric Blais
  date: 2024-03-24
  layout: post
---

Communication complexity is a powerful tool in proving lower bounds or impossibility results in a variety of computational models.


## Definitions

In the basic _two-player communication complexity_ setting, there are two players called Alice and Bob. 
Alice receives some input $x \in \\{0,1\\}^n$. 
Bob receives an input $y \in \\{0,1\\}^n$. 
Alice and Bob want to work together to compute the value $f(x,y)$ of a _target function_ $$f \colon \{0,1\}^n \times \{0,1\}^n \to \{0,1\}$$ on their joint input. 

We can write the function $f$ as a truth table, but it is even more useful to draw it as a matrix, with rows indexed by Alice's inputs and with columns indexed by the possible inputs to Bob.

For example, consider the _equality_ function $$\textsf{EQ} \colon \{0,1\}^n \to \{0,1\}^n \to \{0,1\}$$ defined by $$\textsf{EQ}(x,y) = 1$$ if and only if $x = y$.
Here is the matrix representation of the equality function in the special case where $n = 3$:

| $$\textsf{EQ}$$ | 000 | 001 | 010 | 011 | 100 | 101 | 110 | 111 |
| ------          | --- | --- | --- | --- | --- | --- | --- | --- |
| **000**         | 1   | 0   | 0   | 0   | 0   | 0   | 0   | 0   |
| **001**         | 0   | 1   | 0   | 0   | 0   | 0   | 0   | 0   |
| **010**         | 0   | 0   | 1   | 0   | 0   | 0   | 0   | 0   |
| **011**         | 0   | 0   | 0   | 1   | 0   | 0   | 0   | 0   |
| **100**         | 0   | 0   | 0   | 0   | 1   | 0   | 0   | 0   |
| **101**         | 0   | 0   | 0   | 0   | 0   | 1   | 0   | 0   |
| **110**         | 0   | 0   | 0   | 0   | 0   | 0   | 1   | 0   |
| **111**         | 0   | 0   | 0   | 0   | 0   | 0   | 0   | 1   |

To compute the target function $f$, Alice and Bob define a _communication protocol_. 
This protocol determines who sends the next bit, and for which of their inputs the speaker will send the bit 1 (for the other possible inputs, it sends 0). 
It also determines when Alice and Bob stop exchanging bits and output the value of $f(x,y)$.

We can represent a communication protocol as a rooted binary tree.
Each internal node of the tree is labelled with "A" or "B", according to which player sends the next bit, and the subset of $\\{0,1\\}^n$ for which the player sends the bit 1. 
Leaves are labelled with values $0$ and $1$, representing the output of the protocol.
A protocol _computes_ the function $f$ if and only if the protocol leads Alice and Bob to a leaf labelled with $f(x,y)$ for all possible inputs $x$ and $y$.

The _cost_ of a communication protocol is the depth of the tree that represents it. 
This corresponds to the maximum number of bits that Alice and Bob exchange before outputing the value $f(x,y)$ over all possible inputs that they might get.

> **Definition.**
> The _communication complexity_ of $f \colon \\{0,1\\}^n \times \\{0,1\\}^n \to \\{0,1\\}$, denoted
> 
> $$
> D^{\rm cc}(f),
> $$
> 
> is the minimum cost of a communication protocol that computes $f$.
{: .block-tip}


Every function $$f \colon \{0,1\}^n \times \{0,1\}^n \to \{0,1\}$$ has communication complexity bounded above by $D^{\rm cc}(f) \le n+1$.
That's because we can design a simple protocol in which Alice sends Bob her input $x$ using $n$ bits of communication, Bob computes the value of $f(x,y)$ using all of the information he has, and then Bob sends the value $f(x,y)$.

Some functions can be computed with much less communication.

> **Proposition.**
> The parity function $$\oplus_n \colon \{0,1\}^n \times \{0,1\}^n \to \{0,1\}$$ defined by
> 
> $$
> \oplus_n(x,y) = \Big|\big\{ i \in [n] : x_i \neq y_i \big\}\Big| \pmod{2}
> $$
> 
> has communication complexity $$D^{\rm cc}(\oplus_n) = 2$$.
{: .block-danger}

> **Proof.**
> Noting that $x_i + y_i$ is odd if and only if $x_i \neq y_i$, we can rewrite the $\oplus_n$ function as
> 
> $$
> \oplus_n(x,y) = (x_1 + y_1) + (x_2 + y_2) + \cdots + (x_n + y_n) \pmod{2}
> $$
> 
> and now using associativity and commutativity of addition we can rewrite it again as
> 
> $$
> \oplus_n(x,y) = (x_1 + x_2 + \cdots + x_n) + (y_1 + y_2 + \cdots + y_n) \pmod{2}.
> $$
> 
> So here is a very simple protocol to compute the $\oplus_n$ function: Alice sends a bit corresponding to the parity of its input, then Bob does the same. 
> The label of the leaf is the parity of the sum of the bits that they sent, and it will always equal $\oplus_n(x,y)$.


## Fooling Set Method

For other functions, however, no protocol can do better than the trivial one in which Alice sends her entire input to Bob.
To prove such results, we introduce the _fooling set method_.

> **Definition.**
> A set $$\mathcal{S} \subseteq \{0,1\}^n \times \{0,1\}^n$$ is a _fooling set_ for $$f \colon \{0,1\}^n \times \{0,1\}^n \to \{0,1\}$$ if
> 
> * Every $$(x,y) \in \mathcal{S}$$ satisfies $$f(x,y) = 1$$; and
> * For every $$(x,y)  \neq (x',y') \in \mathcal{S}$$, we have 
> 
> $$
> f(x,y') = 0 \qquad \mbox{or} \qquad f(x',y) = 0.
> $$
{: .block-tip}

Large fooling sets give lower bounds on the deterministic communication complexity of functions.

> **Fooling Set Lemma**
> If there is a fooling set $$\mathcal{S}$$ of cardinality $$|\mathcal{S}| = m$$ for the function $$f$$, then $$D^{\rm cc}(f) \ge \log m.$$
{: .block-danger}

> **Proof.**
> Assume for contradiction that there is a protocol of depth $$d < \log m$$ that computes $$f$$.
> Then the tree that represents the protocol has at most $$2^d < m$$ leaves.
> So by the pigeonhole principle, there must exist two distinct elements $$(x,y) \neq (x',y') \in \mathcal{S}$$ that both end up in the same leaf of this protocol tree.
> Then the inputs $$(x,y')$$ and $$(x',y)$$ also must lead to the same leaf of the protocol because Alice and Bob follow the same path when Alice has either $$x$$ or $$x'$$ and Bob has either $$y$$ or $$y'$$.
> But then there is no label that we can assign to that leaf so that it is consistent with all four of those inputs, so we contradict the claim that the protocol computes $$f$$.

We can use the Fooling Set Lemma to obtain a simple proof that $n$ bits of communication are required to compute the equality function.

> **Theorem.**
> The equality function $$\textsf{EQ} \colon \{0,1\}^n \times \{0,1\}^n \to \{0,1\}$$ 
> has communication complexity $$D^{\rm cc}(\textsf{EQ}) \ge n$$.
{: .block-danger}

> **Proof.**
> Consider the set 
>
> $$
> \mathcal{S} = \Big\{ (x,x) : x \in \{0,1\}^*\Big\}.
> $$
> 
> The set $$\mathcal{S}$$ is a fooling set because for any two distinct strings $$x \neq x' \in \{0,1\}^n$$, we have $$\textsf{EQ}(x,x) = \textsf{EQ}(x',x') = 1$$ but $$\textsf{EQ}(x,x') = \textsf{EQ}(x',x) = 0$$.
> So by the Fooling Set lemma,
> 
> $$
> D^{\rm cc}(\textsf{EQ}) \ge \log |\mathcal{S}| = \log(2^n) = n.
> $$

We can obtain the optimal bound $$D^{\rm cc}(\textsf{EQ}) = n+1$$ with a simple additional observation. 
The Fooling Set Lemma is a bit stronger than what we have used.
Namely, it shows that the number of leaves _labelled 1_ in any protocol tree that computes $$f$$ must be at least $$|\mathcal{S}|$$. 
Since we must have at least one leaf labelled with 0 to compute the same function, every protocol tree has at least $$2^n + 1$$ leaves, which means that it must have depth $$d \ge \log(2^n + 1)$$ that is strictly greater than $$n$$.



## Application: Palindromes

Recall that the _palindrome_ language is the language 

$$
L_{\rm pal} = \Big\{ x \in \{0,1\}^* :  x = x^R \Big\}
$$

of all strings that remain the same when they are reversed.
If we have access to two-tape Turing machines, we can decide $$L_{\rm pal}$$ in linear time.
But what about the standard one-tape Turing machines that we have used to define our $$\mathbf{TIME}$$ complexity classes? 
We can use communication complexity to show that these machines can only decide the palindrome language in time $\Omega(n^2)$.

> **Theorem.**
> For every $c < 2$, $$L_{\rm pal} \notin \mathbf{TIME}(n^c)$$.
{: .block-danger}

> **Proof.**
> We use a reduction proof. 
> Let $T$ be a Turing machine that runs in time $O(n^c)$ and decides $L_{\rm pal}$. 
> We will show how $T$ can be used to obtain a protocol that computes $\textsf{EQ}$ with communication cost $O(n^c)$.
> 
> Specifically, Alice and Bob will define a protocol in which they jointly simulate $T$. 
> First, Alice simulates the machine on input $x0^nx^R$. 
> Let $i \in [n]$ be the index for which $T$ visits the cell $n+i$ the least number of times during its execution. 
> The time complexity of $T$ guarantees that this cell $n+i$ is visited $O(n^{c-1})$ times during $T$'s execution on this input.
> 
> Similarly, Bob runs $T$ on input $y0^ny^R$ to obtain the index $j \in [n]$ for which cell $n+j$ is visited the least number of times during $T$'s execution.
> 
> Alice and Bob exchange the values of $i$ and $j$ that they obtained this way. 
> If these values are different, it must be that $x \neq y$ and so the protocol ends with the value $0$. 
> Otherwise, Alice and Bob jointly simulate $T$ in the following way.
> 
> Alice begins the simulation, keeping track of the symbols on the tape all the way to cell $n+i$. 
> She continues the simulation of $T$ until it goes right to cell $n+i+1$. 
> At this point, she sends Bob the current state that $T$ is in. 
> Then Bob continues the simulation, keeping track of the contents of the tape from cell $n+i+1$ and to the right. 
> When $T$ next goes left to cell $n+i$, he sends Alice the current state of $T$ and she continues the simulation.
> 
> In the end, when $T$ halts, the communication protocol terminates with the value 1 if $T$ accepted and $0$ otherwise.
> 
> This protocol does compute $\textsf{EQ}$ correctly, since it outputs 1 if and only if $T$ accepts (in which case the initial indices will always satisfy $i=j$ and the simulation proceeds correctly), which is the case if and only if $x = y$.
>
> The protocol sends $O(\log n)$ bits to check that the indices $i$ and $j$ matched.
> Then each simulation transfer requires only $O(1)$ bits of communication and there are at most $O(n^{c-1})$ such transfers.
> So the overall communication cost of the protocol is $O(\log n) + O(n^{c-1}) = O(n^{c-1})$.
> 
> From the lower bound we already established on the communication complexity of the equality function, we therefore have that
>
> $$
> n+1 \le D^{\rm cc}(\textsf{EQ}) \le O(n^{c-1}),
> $$
> 
> and therefore we must have $c \ge 2$.

This result gives a hint of the flexibility of the communication complexity method. 
Note in particular that there is no "communication" in the Turing machine model.
And yet, as the proof reveals, a key barrier in deciding the language quickly is an inherent communication task that must be (implicitly) completed to compare the left-hand and right-hand parts of the string.


-----

_Eric Blais &copy;2024 &mdash; Last edited on Mar. 25, 2024_
