## First Order Logic
### Syntax

There are two snytactic categories : **Terms** and **Formulae**.

  `Signature` : consists of a set of function symbols $\mathcal{F}$ and a set of Predicate symbols $\mathcal{P}$. We write $f^k$ or ($p^k$) to indicate function symbol $f$ or (predicate symbol $p$) has arity $k$. 0-arity funciton symbols are treated as constants.
  
 `Term`, **the terms of first-order logic**, is the smallest set where 
  1. $x \in $ Term if $x \in \mathcal{V}$ and
  2. $f^n(t_1, \dots, t_n) \in $ Term if $ f^n \in F$ and $t_i \in$ Term, for all $1 \le i \le n$

  `Form`, **the formulae of first order logic**, is the smallest set where 
  1. $\bot \in Form$
  2. $p^n (t_1, \dots,  t_n) \in Form$ if $p^n \in \mathcal{P}$ and $t_j \in Term$, for all $1 \le j \le n$
  3. $A \circ B \in Form$ if $A \in Form$, $B \in Form$, and $\circ \in \{\land, \lor, \to \}$
  4. $Qx.A \in Form$ if $A \in Form$, $x \in \mathcal{V}$, and $Q \in \{\forall, \exists \}$

Each occurrence of each variable in a formula is **bound** or **free**

$$
\forall x. \exists y. p(x, y) \equiv \forall y. \exists x. p(y, x)
$$

A variable occurrence $x$ in a formula $A$ is **bound** if $x$ occurs within a subformula $B$ of $A$ of the form $\exists x.B$ or $\forall x.B$ and is said to be **free** otherwise.

### Binding and $\alpha$ Conversion
Note that names of bound variables are irrelevant, they just encode the binding structure.

<p align="center">
    <img src="./Image_4.png" width="400px">
</p>

> We can **rename** bound variables at any time : **$\alpha$ conversion** but it must preserve binding structure.

Here's an example : 

$$
\forall x. \exists y. p(x,y) \hspace{1cm} \rightarrow \hspace{1cm} \forall y. \exists x. p(y,x)
$$

### Omitting Parantheses - Binding Strengths
For binary operators :
- $\land$ binds stronger than $\lor$
- $\lor$ binds stronger than $\rightarrow$
- $\rightarrow$ associates to the right
- $\land$ and $\lor$ to the left 
- $\neg$ binds stronger than any other binary operator 

Quantifiers i.e. $\forall$ and $\exists$ extend to the right as far as possible, that is either end of line or `)`.

### Semantics

A **structure** is a pair $S = \langle \mathcal{U}_S, \mathcal{L}_S \rangle$ where $\mathcal{U}_\mathcal{S}$ is an nonempty set, the **universe**, and $\mathcal{I}_S$ is a mapping where :
  1. ${I}_S(p^n)$ is an $n$ -ary relation on $\mathcal{U}_S$ for $p^n \in \mathcal{P}$, and
  2. ${I}_{\mathcal{S}}(f^n)$ is an $n$-ary (total) function on $U_{\mathcal{S}}$, for $f^n \in \mathcal{F}$

As shorthand, we write $p^{\mathcal{S}}$ for $I_{\mathcal{S}}(p)$ and $f^{\mathcal{S}}$ for $I_{\mathcal{S}}(f)$.

An **Intepretation** is a pair $I = \langle \mathcal{S} , v \rangle$, where $\mathcal{S} = \langle U_{\mathcal{S}}, I_{\mathcal{S}} \rangle$ is a structure and $v : \mathcal{V} \to \mathcal{U}_{\mathcal{S}}$ a valuation.

The **value** of a term $t$ under the interpretation $\mathcal{I} = \langle S, v \rangle$ is written as $\mathcal{I} (t)$ and defined by 
  1. $\mathcal{I}(x) = v(x)$ for $x \in \mathcal{V}$ and 
  2. $\mathcal{I}(f(t_1, \dots, t_n)) = f^s(\mathcal{I}(t_1), \dots,\mathcal{I}(t_n) )$

**Satisfiability** : $\vDash \hspace{0.2cm} \subseteq$ Intrepretations $\times$ $Form$ is the smallest relation satisfying 
  - $\langle \mathcal{S}, v \rangle \vDash \exists x. A$ if $\langle \mathcal{S}, v[x \rightarrow a] \rangle \vDash A$ for some $a\in U_\mathcal{S}$
  - $\langle \mathcal{S}, v \rangle \vDash \forall x. A$ if $\langle \mathcal{S}, v[x \rightarrow a] \rangle \vDash A$ for all $a\in U_\mathcal{S}$

Here $v[x \rightarrow a]$ is the valuation $v'$ identical to $v$, except that $v'(X) = a$

- When $\langle \mathcal{S}, v\rangle \vDash A$ we say **$A$ is satisfied with respect to $\langle \mathcal{S}, v\rangle$** or $\langle \mathcal{S}, v\rangle$ is a **model** of $A$.
- When every suitable interpretation is a model, we write $\vDash A$ and say $A$ is **valid**.
- $A$ is **satisfiable** if there is at least one model for $A$ (and contradictory otherwise)
  
Here's an example for a model:
$$
\forall x. p(x, s(x))
$$

- $U_{\mathcal{S}} = \mathcal{N}$
- $p^{\mathcal{S}} = \{(m, n) | m,n \in U_{\mathcal{S}} \text{ and } m < n \}$
- $s^{\mathcal{S}}(x) = x + 1$

### Substitution 
As the name suggest we can replace in $A$ all occurences of a free variable $x$ with some term $t$. We write $A[x \rightarrow t]$ to indicate that we substitute $x$ by $t$ in $A$.

For $ A = \exists y. y \cdot x = x \cdot z$ we can substitute :
$$
A[x \rightarrow 2-1] = \exists y. y \cdot (2-1) = (2-1) \cdot z
$$

Note that all free variables of $t$ must still be free in $A[x \rightarrow t]$ i.e. *Avoid Capture*. If it deems necessary, $\alpha$ convert $A$ before substitution.

### Universal and Existential Quantification
`Universal quantification` :

$$
\frac{\Gamma \vdash A}{\Gamma \vdash \forall x.A} \forall - I ^* \hspace{1cm} \frac{\Gamma \vdash \forall x.A}{\Gamma \vdash A[x \rightarrow t]} \forall - E
$$

with side condition $^* : x$ is not free in any assumption $\Gamma$

`Existential Quantification` : 

$$
\frac{\Gamma \vdash A[x \rightarrow t]}{\Gamma \vdash \exists x.A} \exists - I \hspace{1cm} \frac{\Gamma \vdash \exists x.A \hspace{0.75cm} \Gamma, A \vdash B}{\Gamma \vdash B} \exists - E^*
$$

with side condition $^* : x$ is neither free in $B$ nor free in $\Gamma$

### Equality
Note that we speak of *first-order logic with equality* rather than equality being "just another predicate". We add $t_1 = t_2 \in Form$ if $t_1, t_2 \in Term$

Equality is an equivalence relation shown in these rules 
$$
\frac{}{\Gamma \vdash t = t}ref \hspace{1cm} \frac{\Gamma \vdash t = s}{\Gamma \vdash s = t} sym \hspace{1cm} \frac{\Gamma \vdash t = s \quad \Gamma \vdash s = r}{\Gamma \vdash t = r} trans
$$

Equality is also a congruence on terms and all definable relations
$$
\frac{\Gamma \vdash t_1 = s_1 \hspace{0.5cm} \dots \hspace{0.5cm} \Gamma \vdash t_n = s_n}{\Gamma \vdash f(t_1, \dots , t_n) = f(s_1, \dots, s_n)} \text{cong}_1
$$

$$
\frac{\Gamma \vdash t_1 = s_1  \hspace{0.5cm} \cdots  \hspace{0.5cm} \Gamma \vdash t_n = s_n  \hspace{0.5cm} \Gamma \vdash p(t_1, \dots, , t_n)}{\Gamma \vdash p(s_1, \dots, , s_n)} \text{cong}_2
$$




   