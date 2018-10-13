---
src       : src/plfa/Induction.lagda
title     : "Induction: Proof by Induction"
layout    : page
prev      : /Naturals/
permalink : /Induction/
next      : /Relations/
---

<pre class="Agda">{% raw %}<a id="155" class="Keyword">module</a> <a id="162" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}" class="Module">plfa.Induction</a> <a id="177" class="Keyword">where</a>{% endraw %}</pre>

> Induction makes you feel guilty for getting something out of nothing
> ... but it is one of the greatest ideas of civilization.
> -- Herbert Wilf

Now that we've defined the naturals and operations upon them, our next
step is to learn how to prove properties that they satisfy.  As hinted
by their name, properties of _inductive datatypes_ are proved by
_induction_.


## Imports

We require equivality as in the previous chapter, plus the naturals
and some operations upon them.  We also import a couple of new operations,
`cong`, `sym`, and `_≡⟨_⟩_`, which are explained below.
<pre class="Agda">{% raw %}<a id="790" class="Keyword">import</a> <a id="797" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Relation.Binary.PropositionalEquality</a> <a id="835" class="Symbol">as</a> <a id="838" class="Module">Eq</a>
<a id="841" class="Keyword">open</a> <a id="846" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Eq</a> <a id="849" class="Keyword">using</a> <a id="855" class="Symbol">(</a><a id="856" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">_≡_</a><a id="859" class="Symbol">;</a> <a id="861" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a><a id="865" class="Symbol">;</a> <a id="867" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a><a id="871" class="Symbol">;</a> <a id="873" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a><a id="876" class="Symbol">)</a>
<a id="878" class="Keyword">open</a> <a id="883" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3840" class="Module">Eq.≡-Reasoning</a> <a id="898" class="Keyword">using</a> <a id="904" class="Symbol">(</a><a id="905" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin_</a><a id="911" class="Symbol">;</a> <a id="913" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">_≡⟨⟩_</a><a id="918" class="Symbol">;</a> <a id="920" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">_≡⟨_⟩_</a><a id="926" class="Symbol">;</a> <a id="928" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">_∎</a><a id="930" class="Symbol">)</a>
<a id="932" class="Keyword">open</a> <a id="937" class="Keyword">import</a> <a id="944" href="https://agda.github.io/agda-stdlib/Data.Nat.html" class="Module">Data.Nat</a> <a id="953" class="Keyword">using</a> <a id="959" class="Symbol">(</a><a id="960" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="961" class="Symbol">;</a> <a id="963" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="967" class="Symbol">;</a> <a id="969" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a><a id="972" class="Symbol">;</a> <a id="974" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">_+_</a><a id="977" class="Symbol">;</a> <a id="979" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#433" class="Primitive Operator">_*_</a><a id="982" class="Symbol">;</a> <a id="984" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#320" class="Primitive Operator">_∸_</a><a id="987" class="Symbol">)</a>{% endraw %}</pre>


## Properties of operators

Operators pop up all the time, and mathematicians have agreed
on names for some of the most common properties.

* _Identity_.   Operator `+` has left identity `0` if `0 + n ≡ n`, and
  right identity `0` if `n + 0 ≡ n`, for all `n`. A value that is both
  a left and right identity is just called an identity. Identity is also
  sometimes called _unit_.

* _Associativity_.   Operator `+` is associative if the location
  of parentheses does not matter: `(m + n) + p ≡ m + (n + p)`,
  for all `m`, `n`, and `p`.

* _Commutatitivity_.   Operator `+` is commutative if order or
  arguments does not matter: `m + n ≡ n + m`, for all `m` and `n`.

* _Distributivity_.   Operator `*` distributes over operator `+` from the
  left if `(m + n) * p ≡ (m * p) + (n * p)`, for all `m`, `n`, and `p`,
  and from the right if `m * (p + q) ≡ (m * p) + (m * q)`, for all `m`,
  `p`, and `q`.

Addition has identity `0` and multiplication has identity `1`;
addition and multiplication are both associative and commutative;
and multiplications distributes over addition.

If you ever bump into an operator at a party, you now know how
to make small talk, by asking whether it has a unit and is
associative or commutative.  If you bump into two operators, you
might ask them if one distributes over the other.

Less frivolously, if you ever bump into an operator while reading a
technical paper, this gives you a way to orient yourself, by checking
whether or not it has an identity, is associative or commutative, or
distributes over another operator.  A careful author will ofter call
out these properties---or their lack---for instance by pointing out
that a newly introduced operator is associative but not commutative.

#### Exercise `operators` {#operators}

Give another example of a pair of operators that have an identity
and are associative, commutative, and distribute over one another.

Give an example of an operator that has an identity and is
associative but is not commutative.


## Associativity

One property of addition is that it is _associative_, that is, that the
location of the parentheses does not matter:

    (m + n) + p ≡ m + (n + p)

Here `m`, `n`, and `p` are variables that range over all natural numbers.

We can test the proposition by choosing specific numbers for the three
variables.
<pre class="Agda">{% raw %}<a id="3346" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#3346" class="Function">_</a> <a id="3348" class="Symbol">:</a> <a id="3350" class="Symbol">(</a><a id="3351" class="Number">3</a> <a id="3353" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3355" class="Number">4</a><a id="3356" class="Symbol">)</a> <a id="3358" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3360" class="Number">5</a> <a id="3362" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="3364" class="Number">3</a> <a id="3366" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3368" class="Symbol">(</a><a id="3369" class="Number">4</a> <a id="3371" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3373" class="Number">5</a><a id="3374" class="Symbol">)</a>
<a id="3376" class="Symbol">_</a> <a id="3378" class="Symbol">=</a>
  <a id="3382" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="3392" class="Symbol">(</a><a id="3393" class="Number">3</a> <a id="3395" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3397" class="Number">4</a><a id="3398" class="Symbol">)</a> <a id="3400" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3402" class="Number">5</a>
  <a id="3406" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3414" class="Number">7</a> <a id="3416" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3418" class="Number">5</a>
  <a id="3422" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3430" class="Number">12</a>
  <a id="3435" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3443" class="Number">3</a> <a id="3445" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3447" class="Number">9</a>
  <a id="3451" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="3459" class="Number">3</a> <a id="3461" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3463" class="Symbol">(</a><a id="3464" class="Number">4</a> <a id="3466" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="3468" class="Number">5</a><a id="3469" class="Symbol">)</a>
  <a id="3473" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
Here we have displayed the computation as a chain of equations,
one term to a line.  It is often easiest to read such chains from the top down
until one reaches the simplest term (in this case, `12`), and
then from the bottom up until one reaches the same term.

The test reveals that associativity is perhaps not as obvious as first
it appears.  Why should `7 + 5` be the same as `3 + 9`?  We might want
to gather more evidence, testing the proposition by choosing other
numbers.  But---since there are an infinite number of
naturals---testing can never be complete.  Is there any way we can be
sure that associativity holds for _all_ the natural numbers?

The answer is yes! We can prove a property holds for all naturals using
_proof by induction_.


## Proof by induction

Recall the definition of natural numbers consists of a _base case_
which tells us that `zero` is a natural, and an _inductive case_
which tells us that if `m` is a natural then `suc m` is also a natural.

Proof by induction follows the structure of this definition.  To prove
a property of natural numbers by induction, we need prove two cases.
First is the _base case_, where we show the property holds for `zero`.
Second is the _inductive case_, where we assume the the property holds for
an arbitrary natural `m` (we call this the _inductive hypothesis_), and
then show that the property must also hold for `suc m`.

If we write `P m` for a property of `m`, then what we need to
demonstrate are the following two inference rules:

    ------
    P zero

    P m
    ---------
    P (suc m)

Let's unpack these rules.  The first rule is the base case, and
requires we show that property `P` holds for `zero`.  The second rule
is the inductive case, and requires we show that if we assume the
inductive hypothesis---namely that `P` holds for `m`---then it follows that
`P` also holds for `suc m`.

Why does this work?  Again, it can be explained by a creation story.
To start with, we know no properties.

    -- in the beginning, no properties are known

Now, we apply the two rules to all the properties we know about.  The
base case tells us that `P zero` holds, so we add it to the set of
known properties.  The inductive case tells us that if `P m` holds (on
the day before today) then `P (suc m)` also holds (today).  We didn't
know about any properties before today, so the inductive case doesn't
apply.

    -- on the first day, one property is known
    P zero

Then we repeat the process, so on the next day we know about all the
properties from the day before, plus any properties added by the rules.
The base case tells us that `P zero` holds, but we already
knew that. But now the inductive case tells us that since `P zero`
held yesterday, then `P (suc zero)` holds today.

    -- on the second day, two properties are known
    P zero
    P (suc zero)

And we repeat the process again. Now the inductive case
tells us that since `P zero` and `P (suc zero)` both hold, then
`P (suc zero)` and `P (suc (suc zero))` also hold. We already knew about
the first of these, but the second is new.

    -- on the third day, three properties are known
    P zero
    P (suc zero)
    P (suc (suc zero))

You've got the hang of it by now.

    -- on the fourth day, four properties are known
    P zero
    P (suc zero)
    P (suc (suc zero))
    P (suc (suc (suc zero)))

The process continues.  On the _n_'th day there will be _n_ distinct
properties that hold.  The property of every natural number will appear
on some given day.  In particular, the property `P n` first appears on
day _n+1_.


## Our first proof: associativity

To prove associativity, we take `P m` to be the property

    (m + n) + p ≡ m + (n + p)

Here `n` and `p` are arbitrary natural numbers, so if we can show the
equation holds for all `m` it will also hold for all `n` and `p`.
The appropriate instances of the inference rules are:

    -------------------------------
    (zero + n) + p ≡ zero + (n + p)

    (m + n) + p ≡ m + (n + p)
    ---------------------------------
    (suc m + n) + p ≡ suc m + (n + p)

If we can demonstrate both of these, then associativity of addition
follows by induction.

Here is the proposition's statement and proof.
<pre class="Agda">{% raw %}<a id="+-assoc"></a><a id="7712" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="7720" class="Symbol">:</a> <a id="7722" class="Symbol">∀</a> <a id="7724" class="Symbol">(</a><a id="7725" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7725" class="Bound">m</a> <a id="7727" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7727" class="Bound">n</a> <a id="7729" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7729" class="Bound">p</a> <a id="7731" class="Symbol">:</a> <a id="7733" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="7734" class="Symbol">)</a> <a id="7736" class="Symbol">→</a> <a id="7738" class="Symbol">(</a><a id="7739" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7725" class="Bound">m</a> <a id="7741" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7743" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7727" class="Bound">n</a><a id="7744" class="Symbol">)</a> <a id="7746" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7748" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7729" class="Bound">p</a> <a id="7750" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="7752" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7725" class="Bound">m</a> <a id="7754" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7756" class="Symbol">(</a><a id="7757" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7727" class="Bound">n</a> <a id="7759" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7761" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7729" class="Bound">p</a><a id="7762" class="Symbol">)</a>
<a id="7764" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="7772" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="7777" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7777" class="Bound">n</a> <a id="7779" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7779" class="Bound">p</a> <a id="7781" class="Symbol">=</a>
  <a id="7785" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="7795" class="Symbol">(</a><a id="7796" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="7801" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7803" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7777" class="Bound">n</a><a id="7804" class="Symbol">)</a> <a id="7806" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7808" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7779" class="Bound">p</a>
  <a id="7812" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="7820" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7777" class="Bound">n</a> <a id="7822" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7824" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7779" class="Bound">p</a>
  <a id="7828" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
   <a id="7835" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="7840" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7842" class="Symbol">(</a><a id="7843" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7777" class="Bound">n</a> <a id="7845" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7847" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7779" class="Bound">p</a><a id="7848" class="Symbol">)</a>
  <a id="7852" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="7854" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="7862" class="Symbol">(</a><a id="7863" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7867" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a><a id="7868" class="Symbol">)</a> <a id="7870" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a> <a id="7872" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a> <a id="7874" class="Symbol">=</a>
  <a id="7878" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="7888" class="Symbol">(</a><a id="7889" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7893" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a> <a id="7895" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7897" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a><a id="7898" class="Symbol">)</a> <a id="7900" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7902" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a>
  <a id="7906" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="7914" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7918" class="Symbol">(</a><a id="7919" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a> <a id="7921" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7923" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a><a id="7924" class="Symbol">)</a> <a id="7926" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7928" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a>
  <a id="7932" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="7940" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7944" class="Symbol">((</a><a id="7946" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a> <a id="7948" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7950" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a><a id="7951" class="Symbol">)</a> <a id="7953" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="7955" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a><a id="7956" class="Symbol">)</a>
  <a id="7960" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="7963" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="7968" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7972" class="Symbol">(</a><a id="7973" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="7981" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a> <a id="7983" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a> <a id="7985" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a><a id="7986" class="Symbol">)</a> <a id="7988" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="7994" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="7998" class="Symbol">(</a><a id="7999" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a> <a id="8001" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8003" class="Symbol">(</a><a id="8004" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a> <a id="8006" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8008" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a><a id="8009" class="Symbol">))</a>
  <a id="8014" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="8022" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8026" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7867" class="Bound">m</a> <a id="8028" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8030" class="Symbol">(</a><a id="8031" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7870" class="Bound">n</a> <a id="8033" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="8035" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7872" class="Bound">p</a><a id="8036" class="Symbol">)</a>
  <a id="8040" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
We have named the proof `+-assoc`.  In Agda, identifiers can consist of
any sequence of characters not including spaces or the characters `@.(){};_`.

Let's unpack this code.  The signature states that we are
defining the identifier `+-assoc` which provides evidence for the
proposition:

    ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)

The upside down A is pronounced "for all", and the proposition
asserts that for all natural numbers `m`, `n`, and `p` that
the equation `(m + n) + p ≡ m + (n + p)` holds.  Evidence for the proposition
is a function that accepts three natural numbers, binds them to `m`, `n`, and `p`,
and returns evidence for the corresponding instance of the equation.

For the base case, we must show:

    (zero + n) + p ≡ zero + (n + p)

Simplifying both sides with the base case of addition yields the equation:

    n + p ≡ n + p

This holds trivially.  Reading the chain of equations in the base case of the proof,
the top and bottom of the chain match the two sides of the equation to
be shown, and reading down from the top and up from the bottom takes us to
`n + p` in the middle.  No justification other than simplification is required.

For the inductive case, we must show:

    (suc m + n) + p ≡ suc m + (n + p)

Simplifying both sides with the inductive case of addition yields the equation:

    suc ((m + n) + p) ≡ suc (m + (n + p))

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    (m + n) + p ≡ m + (n + p)

Reading the chain of equations in the inductive case of the proof, the
top and bottom of the chain match the two sides of the equation to be
shown, and reading down from the top and up from the bottom takes us
to the simplified equation above. The remaining equation, does not follow
from simplification alone, so we use an additional operator for chain
reasoning, `_≡⟨_⟩_`, where a justification for the equation appears
within angle brackets.  The justification given is:

    ⟨ cong suc (+-assoc m n p) ⟩

Here, the recursive invocation `+-assoc m n p` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.

A relation is said to be a _congruence_ for a given function if it is
preserved by applying that function.  If `e` is evidence that `x ≡ y`,
then `cong f e` is evidence that `f x ≡ f y`, for any function `f`.

Here the inductive hypothesis is not assumed, but instead proved by a
recursive invocation of the function we are defining, `+-assoc m n p`.
As with addition, this is well founded because associativity of
larger numbers is proved in terms of associativity of smaller numbers.
In this case, `assoc (suc m) n p` is proved using `assoc m n p`.
The correspondence between proof by induction and definition by
recursion is one of the most appealing aspects of Agda.


## Our second proof: commutativity

Another important property of addition is that it is _commutative_, that is,
that the order of the operands does not matter:

    m + n ≡ n + m

The proof requires that we first demonstrate two lemmas.

### The first lemma

The base case of the definition of addition states that zero
is a left-identity:

    zero + n ≡ n

Our first lemma states that zero is also a right-identity:

    m + zero ≡ m

Here is the lemma's statement and proof.
<pre class="Agda">{% raw %}<a id="+-identityʳ"></a><a id="11375" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11375" class="Function">+-identityʳ</a> <a id="11387" class="Symbol">:</a> <a id="11389" class="Symbol">∀</a> <a id="11391" class="Symbol">(</a><a id="11392" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11392" class="Bound">m</a> <a id="11394" class="Symbol">:</a> <a id="11396" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11397" class="Symbol">)</a> <a id="11399" class="Symbol">→</a> <a id="11401" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11392" class="Bound">m</a> <a id="11403" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11405" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="11410" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="11412" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11392" class="Bound">m</a>
<a id="11414" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11375" class="Function">+-identityʳ</a> <a id="11426" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="11431" class="Symbol">=</a>
  <a id="11435" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="11445" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="11450" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11452" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="11459" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="11467" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="11474" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="11476" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11375" class="Function">+-identityʳ</a> <a id="11488" class="Symbol">(</a><a id="11489" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11493" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11493" class="Bound">m</a><a id="11494" class="Symbol">)</a> <a id="11496" class="Symbol">=</a>
  <a id="11500" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="11510" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11493" class="Bound">m</a> <a id="11516" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11518" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="11525" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="11533" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11537" class="Symbol">(</a><a id="11538" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11493" class="Bound">m</a> <a id="11540" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="11542" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="11546" class="Symbol">)</a>
  <a id="11550" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="11553" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="11558" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11562" class="Symbol">(</a><a id="11563" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11375" class="Function">+-identityʳ</a> <a id="11575" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11493" class="Bound">m</a><a id="11576" class="Symbol">)</a> <a id="11578" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="11584" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="11588" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11493" class="Bound">m</a>
  <a id="11592" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
The signature states that we are defining the identifier `+-identityʳ` which
provides evidence for the proposition:

    ∀ (m : ℕ) → m + zero ≡ m

Evidence for the proposition is a function that accepts a natural
number, binds it to `m`, and returns evidence for the corresponding
instance of the equation.  The proof is by induction on `m`.

For the base case, we must show:

    zero + zero ≡ zero

Simplifying with the base case of addition, this is straightforward.

For the inductive case, we must show:

    (suc m) + zero = suc m

Simplifying both sides with the inductive case of addition yields the equation:

    suc (m + zero) = suc m

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    m + zero ≡ m

Reading the chain of equations down from the top and up from the bottom
takes us to the simplified equation above.  The remaining
equation has the justification:

    ⟨ cong suc (+-identityʳ m) ⟩

Here, the recursive invocation `+-identityʳ m` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.  This completes the first lemma.

### The second lemma

The inductive case of the definition of addition pushes `suc` on the
first argument to the outside:

    suc m + n ≡ suc (m + n)

Our second lemma does the same for `suc` on the second argument:

    m + suc n ≡ suc (m + n)

Here is the lemma's statement and proof.
<pre class="Agda">{% raw %}<a id="+-suc"></a><a id="13048" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Function">+-suc</a> <a id="13054" class="Symbol">:</a> <a id="13056" class="Symbol">∀</a> <a id="13058" class="Symbol">(</a><a id="13059" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13059" class="Bound">m</a> <a id="13061" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13061" class="Bound">n</a> <a id="13063" class="Symbol">:</a> <a id="13065" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="13066" class="Symbol">)</a> <a id="13068" class="Symbol">→</a> <a id="13070" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13059" class="Bound">m</a> <a id="13072" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13074" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13078" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13061" class="Bound">n</a> <a id="13080" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="13082" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13086" class="Symbol">(</a><a id="13087" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13059" class="Bound">m</a> <a id="13089" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13091" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13061" class="Bound">n</a><a id="13092" class="Symbol">)</a>
<a id="13094" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Function">+-suc</a> <a id="13100" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="13105" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13105" class="Bound">n</a> <a id="13107" class="Symbol">=</a>
  <a id="13111" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="13121" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="13126" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13128" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13132" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13105" class="Bound">n</a>
  <a id="13136" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13144" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13148" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13105" class="Bound">n</a>
  <a id="13152" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13160" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13164" class="Symbol">(</a><a id="13165" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="13170" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13172" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13105" class="Bound">n</a><a id="13173" class="Symbol">)</a>
  <a id="13177" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="13179" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Function">+-suc</a> <a id="13185" class="Symbol">(</a><a id="13186" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13190" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13190" class="Bound">m</a><a id="13191" class="Symbol">)</a> <a id="13193" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13193" class="Bound">n</a> <a id="13195" class="Symbol">=</a>
  <a id="13199" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="13209" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13213" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13190" class="Bound">m</a> <a id="13215" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13217" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13221" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13193" class="Bound">n</a>
  <a id="13225" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13233" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13237" class="Symbol">(</a><a id="13238" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13190" class="Bound">m</a> <a id="13240" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13242" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13246" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13193" class="Bound">n</a><a id="13247" class="Symbol">)</a>
  <a id="13251" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="13254" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="13259" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13263" class="Symbol">(</a><a id="13264" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Function">+-suc</a> <a id="13270" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13190" class="Bound">m</a> <a id="13272" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13193" class="Bound">n</a><a id="13273" class="Symbol">)</a> <a id="13275" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="13281" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13285" class="Symbol">(</a><a id="13286" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13290" class="Symbol">(</a><a id="13291" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13190" class="Bound">m</a> <a id="13293" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13295" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13193" class="Bound">n</a><a id="13296" class="Symbol">))</a>
  <a id="13301" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="13309" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13313" class="Symbol">(</a><a id="13314" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13318" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13190" class="Bound">m</a> <a id="13320" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="13322" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13193" class="Bound">n</a><a id="13323" class="Symbol">)</a>
  <a id="13327" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
The signature states that we are defining the identifier `+-suc` which provides
evidence for the proposition:

    ∀ (m n : ℕ) → m + suc n ≡ suc (m + n)

Evidence for the proposition is a function that accepts two natural numbers,
binds them to `m` and `n`, and returns evidence for the corresponding instance
of the equation.  The proof is by induction on `m`.

For the base case, we must show:

    zero + suc n ≡ suc (zero + n)

Simplifying with the base case of addition, this is straightforward.

For the inductive case, we must show:

    suc m + suc n ≡ suc (suc m + n)

Simplifying both sides with the inductive case of addition yields the equation:

    suc (m + suc n) ≡ suc (suc (m + n))

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    m + suc n ≡ suc (m + n)

Reading the chain of equations down from the top and up from the bottom
takes us to the simplified equation in the middle.  The remaining
equation has the justification:

    ⟨ cong suc (+-suc m n) ⟩

Here, the recursive invocation `+-suc m n` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.  This completes the second lemma.

### The proposition

Finally, here is our proposition's statement and proof.
<pre class="Agda">{% raw %}<a id="+-comm"></a><a id="14637" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14637" class="Function">+-comm</a> <a id="14644" class="Symbol">:</a> <a id="14646" class="Symbol">∀</a> <a id="14648" class="Symbol">(</a><a id="14649" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14649" class="Bound">m</a> <a id="14651" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14651" class="Bound">n</a> <a id="14653" class="Symbol">:</a> <a id="14655" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="14656" class="Symbol">)</a> <a id="14658" class="Symbol">→</a> <a id="14660" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14649" class="Bound">m</a> <a id="14662" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14664" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14651" class="Bound">n</a> <a id="14666" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="14668" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14651" class="Bound">n</a> <a id="14670" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14672" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14649" class="Bound">m</a>
<a id="14674" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14637" class="Function">+-comm</a> <a id="14681" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14681" class="Bound">m</a> <a id="14683" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="14688" class="Symbol">=</a>
  <a id="14692" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="14702" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14681" class="Bound">m</a> <a id="14704" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14706" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>
  <a id="14713" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="14716" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#11375" class="Function">+-identityʳ</a> <a id="14728" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14681" class="Bound">m</a> <a id="14730" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="14736" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14681" class="Bound">m</a>
  <a id="14740" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="14748" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="14753" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14755" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14681" class="Bound">m</a>
  <a id="14759" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>
<a id="14761" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14637" class="Function">+-comm</a> <a id="14768" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a> <a id="14770" class="Symbol">(</a><a id="14771" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14775" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a><a id="14776" class="Symbol">)</a> <a id="14778" class="Symbol">=</a>
  <a id="14782" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="14792" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a> <a id="14794" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14796" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14800" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a>
  <a id="14804" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="14807" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#13048" class="Function">+-suc</a> <a id="14813" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a> <a id="14815" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a> <a id="14817" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="14823" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14827" class="Symbol">(</a><a id="14828" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a> <a id="14830" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14832" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a><a id="14833" class="Symbol">)</a>
  <a id="14837" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="14840" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="14845" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14849" class="Symbol">(</a><a id="14850" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14637" class="Function">+-comm</a> <a id="14857" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a> <a id="14859" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a><a id="14860" class="Symbol">)</a> <a id="14862" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="14868" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14872" class="Symbol">(</a><a id="14873" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a> <a id="14875" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14877" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a><a id="14878" class="Symbol">)</a>
  <a id="14882" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3999" class="Function Operator">≡⟨⟩</a>
    <a id="14890" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14894" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14775" class="Bound">n</a> <a id="14896" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="14898" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#14768" class="Bound">m</a>
  <a id="14902" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
The first line states that we are defining the identifier
`+-comm` which provides evidence for the proposition:

    ∀ (m n : ℕ) → m + n ≡ n + m

Evidence for the proposition is a function that accepts two
natural numbers, binds them to `m` and `n`, and returns evidence for the
corresponding instance of the equation.  The proof is by
induction on `n`.  (Not on `m` this time!)

For the base case, we must show:

    m + zero ≡ zero + m

Simplifying both sides with the base case of addition yields the equation:

    m + zero ≡ m

The the remaining equation has the justification `⟨ +-identityʳ m ⟩`,
which invokes the first lemma.

For the inductive case, we must show:

    m + suc n ≡ suc n + m

Simplifying both sides with the inductive case of addition yields the equation:

    m + suc n ≡ suc (n + m)

We show this in two steps.  First, we have:

    m + suc n ≡ suc (m + n)

which is justified by the second lemma, `⟨ +-suc m n ⟩`.  Then we
have

    suc (m + n) ≡ suc (n + m)

which is justified by congruence and the induction hypothesis,
`⟨ cong suc (+-comm m n) ⟩`.  This completes the proof.

Agda requires that identifiers are defined before they are used,
so we must present the lemmas before the main proposition, as we
have done above.  In practice, one will often attempt to prove
the main proposition first, and the equations required to do so
will suggest what lemmas to prove.


## Our first corollary: rearranging {#sections}

We can apply associativity to rearrange parentheses however we like.
Here is an example.
<pre class="Agda">{% raw %}<a id="+-rearrange"></a><a id="16468" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16468" class="Function">+-rearrange</a> <a id="16480" class="Symbol">:</a> <a id="16482" class="Symbol">∀</a> <a id="16484" class="Symbol">(</a><a id="16485" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16485" class="Bound">m</a> <a id="16487" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16487" class="Bound">n</a> <a id="16489" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16489" class="Bound">p</a> <a id="16491" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16491" class="Bound">q</a> <a id="16493" class="Symbol">:</a> <a id="16495" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="16496" class="Symbol">)</a> <a id="16498" class="Symbol">→</a> <a id="16500" class="Symbol">(</a><a id="16501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16485" class="Bound">m</a> <a id="16503" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16505" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16487" class="Bound">n</a><a id="16506" class="Symbol">)</a> <a id="16508" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16510" class="Symbol">(</a><a id="16511" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16489" class="Bound">p</a> <a id="16513" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16515" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16491" class="Bound">q</a><a id="16516" class="Symbol">)</a> <a id="16518" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="16520" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16485" class="Bound">m</a> <a id="16522" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16524" class="Symbol">(</a><a id="16525" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16487" class="Bound">n</a> <a id="16527" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16529" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16489" class="Bound">p</a><a id="16530" class="Symbol">)</a> <a id="16532" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16534" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16491" class="Bound">q</a>
<a id="16536" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16468" class="Function">+-rearrange</a> <a id="16548" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16550" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16552" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a> <a id="16554" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a> <a id="16556" class="Symbol">=</a>
  <a id="16560" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#3941" class="Function Operator">begin</a>
    <a id="16570" class="Symbol">(</a><a id="16571" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16573" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16575" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a><a id="16576" class="Symbol">)</a> <a id="16578" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16580" class="Symbol">(</a><a id="16581" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a> <a id="16583" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16585" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a><a id="16586" class="Symbol">)</a>
  <a id="16590" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="16593" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="16601" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16603" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16605" class="Symbol">(</a><a id="16606" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a> <a id="16608" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16610" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a><a id="16611" class="Symbol">)</a> <a id="16613" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="16619" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16621" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16623" class="Symbol">(</a><a id="16624" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16626" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16628" class="Symbol">(</a><a id="16629" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a> <a id="16631" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16633" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a><a id="16634" class="Symbol">))</a>
  <a id="16639" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="16642" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="16647" class="Symbol">(</a><a id="16648" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16650" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+_</a><a id="16652" class="Symbol">)</a> <a id="16654" class="Symbol">(</a><a id="16655" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a> <a id="16659" class="Symbol">(</a><a id="16660" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="16668" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16670" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a> <a id="16672" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a><a id="16673" class="Symbol">))</a> <a id="16676" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="16682" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16684" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16686" class="Symbol">((</a><a id="16688" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16690" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16692" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a><a id="16693" class="Symbol">)</a> <a id="16695" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16697" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a><a id="16698" class="Symbol">)</a>
  <a id="16702" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">≡⟨</a> <a id="16705" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a> <a id="16709" class="Symbol">(</a><a id="16710" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#7712" class="Function">+-assoc</a> <a id="16718" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16720" class="Symbol">(</a><a id="16721" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16723" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16725" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a><a id="16726" class="Symbol">)</a> <a id="16728" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a><a id="16729" class="Symbol">)</a> <a id="16731" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4058" class="Function Operator">⟩</a>
    <a id="16737" class="Symbol">(</a><a id="16738" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16548" class="Bound">m</a> <a id="16740" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16742" class="Symbol">(</a><a id="16743" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16550" class="Bound">n</a> <a id="16745" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16747" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16552" class="Bound">p</a><a id="16748" class="Symbol">))</a> <a id="16751" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16753" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#16554" class="Bound">q</a>
  <a id="16757" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#4239" class="Function Operator">∎</a>{% endraw %}</pre>
No induction is required, we simply apply associativity twice.
A few points are worthy of note.

First, addition associates to the left, so `m + (n + p) + q`
stands for `(m + (n + p)) + q`.

Second, we use `sym` to interchange the sides of an equation.
Proposition `+-assoc n p q` shifts parentheses from right to left:

    (n + p) + q ≡ n + (p + q)

To shift them the other way, we use `sym (+-assoc m n p)`:

    n + (p + q) ≡ (n + p) + q

In general, if `e` provides evidence for `x ≡ y` then `sym e` provides
evidence for `y ≡ x`.

Third, Agda supports a variant of the _section_ notation introduced by
Richard Bird.  We write `(x +_)` for the function that applied to `y`
returns `x + y`.  Thus, applying the congruence `cong (m +_)` takes
the above equation into

    m + (n + (p + q)) ≡ m + ((n + p) + q)

Similarly, we write `(_+ x)` for the function that applied to `y`
returns `y + x`; the same works for any infix operator.



## Creation, one last time

Returning to the proof of associativity, it may be helpful to view the inductive
proof (or, equivalently, the recursive definition) as a creation story.  This
time we are concerned with judgements asserting associativity.

     -- in the beginning, we know nothing about associativity

Now, we apply the rules to all the judgements we know about.  The base
case tells us that `(zero + n) + p ≡ zero + (n + p)` for every natural
`n` and `p`.  The inductive case tells us that if `(m + n) + p ≡ m +
(n + p)` (on the day before today) then
`(suc m + n) + p ≡ suc m + (n + p)` (today).
We didn't know any judgments about associativity before today, so that
rule doesn't give us any new judgments.

    -- on the first day, we know about associativity of 0
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...

Then we repeat the process, so on the next day we know about all the
judgements from the day before, plus any judgements added by the rules.
The base case tells us nothing new, but now the inductive case adds
more judgements.

    -- on the second day, we know about associativity of 0 and 1
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...

And we repeat the process again.

    -- on the third day, we know about associativity of 0, 1, and 2
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...
    (2 + 0) + 0 ≡ 2 + (0 + 0)   ...   (2 + 4) + 5 ≡ 2 + (4 + 5)   ...

You've got the hang of it by now.

    -- on the fourth day, we know about associativity of 0, 1, 2, and 3
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...
    (2 + 0) + 0 ≡ 2 + (0 + 0)   ...   (2 + 4) + 5 ≡ 2 + (4 + 5)   ...
    (3 + 0) + 0 ≡ 3 + (0 + 0)   ...   (3 + 4) + 5 ≡ 3 + (4 + 5)   ...

The process continues.  On the _m_'th day we will know all the
judgements where the first number is less than _m_.

There is also a completely finite approach to generating the same equations,
which is left as an exercise for the reader.

#### Exercise `finite-+-assoc` (stretch) {#finite-plus-assoc}

Write out what is known about associativity of addition on each of the first four
days using a finite story of creation, as
[earlier]({{ site.baseurl }}{% link out/plfa/Naturals.md%}#finite-creation)

## Associativity with rewrite

There is more than one way to skin a cat.  Here is a second proof of
associativity of addition in Agda, using `rewrite` rather than chains of
equations.
<pre class="Agda">{% raw %}<a id="+-assoc′"></a><a id="20359" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20359" class="Function">+-assoc′</a> <a id="20368" class="Symbol">:</a> <a id="20370" class="Symbol">∀</a> <a id="20372" class="Symbol">(</a><a id="20373" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20373" class="Bound">m</a> <a id="20375" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20375" class="Bound">n</a> <a id="20377" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20377" class="Bound">p</a> <a id="20379" class="Symbol">:</a> <a id="20381" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="20382" class="Symbol">)</a> <a id="20384" class="Symbol">→</a> <a id="20386" class="Symbol">(</a><a id="20387" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20373" class="Bound">m</a> <a id="20389" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20391" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20375" class="Bound">n</a><a id="20392" class="Symbol">)</a> <a id="20394" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20396" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20377" class="Bound">p</a> <a id="20398" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="20400" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20373" class="Bound">m</a> <a id="20402" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20404" class="Symbol">(</a><a id="20405" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20375" class="Bound">n</a> <a id="20407" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="20409" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20377" class="Bound">p</a><a id="20410" class="Symbol">)</a>
<a id="20412" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20359" class="Function">+-assoc′</a> <a id="20421" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="20429" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20429" class="Bound">n</a> <a id="20431" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20431" class="Bound">p</a>                          <a id="20458" class="Symbol">=</a>  <a id="20461" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="20466" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20359" class="Function">+-assoc′</a> <a id="20475" class="Symbol">(</a><a id="20476" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="20480" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20480" class="Bound">m</a><a id="20481" class="Symbol">)</a> <a id="20483" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20483" class="Bound">n</a> <a id="20485" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20485" class="Bound">p</a>  <a id="20488" class="Keyword">rewrite</a> <a id="20496" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20359" class="Function">+-assoc′</a> <a id="20505" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20480" class="Bound">m</a> <a id="20507" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20483" class="Bound">n</a> <a id="20509" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#20485" class="Bound">p</a>  <a id="20512" class="Symbol">=</a>  <a id="20515" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>{% endraw %}</pre>

For the base case, we must show:

    (zero + n) + p ≡ zero + (n + p)

Simplifying both sides with the base case of addition yields the equation:

    n + p ≡ n + p

This holds trivially. The proof that a term is equal to itself is written `refl`.

For the inductive case, we must show:

    (suc m + n) + p ≡ suc m + (n + p)

Simplifying both sides with the inductive case of addition yields the equation:

    suc ((m + n) + p) ≡ suc (m + (n + p))

After rewriting with the inductive hypothesis these two terms are equal, and the
proof is again given by `refl`.  Rewriting by a given equation is indicated by
the keyword `rewrite` followed by a proof of that equation.  Rewriting avoids
not only chains of equations but also the need to invoke `cong`.


## Commutativity with rewrite

Here is a second proof of commutativity of addition, using `rewrite` rather than
chains of equations.
<pre class="Agda">{% raw %}<a id="+-identity′"></a><a id="21434" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21434" class="Function">+-identity′</a> <a id="21446" class="Symbol">:</a> <a id="21448" class="Symbol">∀</a> <a id="21450" class="Symbol">(</a><a id="21451" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21451" class="Bound">n</a> <a id="21453" class="Symbol">:</a> <a id="21455" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21456" class="Symbol">)</a> <a id="21458" class="Symbol">→</a> <a id="21460" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21451" class="Bound">n</a> <a id="21462" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21464" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21469" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="21471" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21451" class="Bound">n</a>
<a id="21473" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21434" class="Function">+-identity′</a> <a id="21485" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21490" class="Symbol">=</a> <a id="21492" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="21497" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21434" class="Function">+-identity′</a> <a id="21509" class="Symbol">(</a><a id="21510" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21514" class="Bound">n</a><a id="21515" class="Symbol">)</a> <a id="21517" class="Keyword">rewrite</a> <a id="21525" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21434" class="Function">+-identity′</a> <a id="21537" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21514" class="Bound">n</a> <a id="21539" class="Symbol">=</a> <a id="21541" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>

<a id="+-suc′"></a><a id="21547" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21547" class="Function">+-suc′</a> <a id="21554" class="Symbol">:</a> <a id="21556" class="Symbol">∀</a> <a id="21558" class="Symbol">(</a><a id="21559" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21559" class="Bound">m</a> <a id="21561" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21561" class="Bound">n</a> <a id="21563" class="Symbol">:</a> <a id="21565" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21566" class="Symbol">)</a> <a id="21568" class="Symbol">→</a> <a id="21570" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21559" class="Bound">m</a> <a id="21572" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21574" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21578" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21561" class="Bound">n</a> <a id="21580" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="21582" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21586" class="Symbol">(</a><a id="21587" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21559" class="Bound">m</a> <a id="21589" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21591" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21561" class="Bound">n</a><a id="21592" class="Symbol">)</a>
<a id="21594" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21547" class="Function">+-suc′</a> <a id="21601" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21606" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21606" class="Bound">n</a> <a id="21608" class="Symbol">=</a> <a id="21610" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="21615" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21547" class="Function">+-suc′</a> <a id="21622" class="Symbol">(</a><a id="21623" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21627" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21627" class="Bound">m</a><a id="21628" class="Symbol">)</a> <a id="21630" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21630" class="Bound">n</a> <a id="21632" class="Keyword">rewrite</a> <a id="21640" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21547" class="Function">+-suc′</a> <a id="21647" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21627" class="Bound">m</a> <a id="21649" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21630" class="Bound">n</a> <a id="21651" class="Symbol">=</a> <a id="21653" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>

<a id="+-comm′"></a><a id="21659" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21659" class="Function">+-comm′</a> <a id="21667" class="Symbol">:</a> <a id="21669" class="Symbol">∀</a> <a id="21671" class="Symbol">(</a><a id="21672" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21672" class="Bound">m</a> <a id="21674" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21674" class="Bound">n</a> <a id="21676" class="Symbol">:</a> <a id="21678" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21679" class="Symbol">)</a> <a id="21681" class="Symbol">→</a> <a id="21683" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21672" class="Bound">m</a> <a id="21685" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21687" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21674" class="Bound">n</a> <a id="21689" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="21691" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21674" class="Bound">n</a> <a id="21693" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21695" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21672" class="Bound">m</a>
<a id="21697" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21659" class="Function">+-comm′</a> <a id="21705" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21705" class="Bound">m</a> <a id="21707" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="21712" class="Keyword">rewrite</a> <a id="21720" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21434" class="Function">+-identity′</a> <a id="21732" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21705" class="Bound">m</a> <a id="21734" class="Symbol">=</a> <a id="21736" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="21741" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21659" class="Function">+-comm′</a> <a id="21749" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21749" class="Bound">m</a> <a id="21751" class="Symbol">(</a><a id="21752" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="21756" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21756" class="Bound">n</a><a id="21757" class="Symbol">)</a> <a id="21759" class="Keyword">rewrite</a> <a id="21767" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21547" class="Function">+-suc′</a> <a id="21774" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21749" class="Bound">m</a> <a id="21776" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21756" class="Bound">n</a> <a id="21778" class="Symbol">|</a> <a id="21780" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21659" class="Function">+-comm′</a> <a id="21788" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21749" class="Bound">m</a> <a id="21790" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#21756" class="Bound">n</a> <a id="21792" class="Symbol">=</a> <a id="21794" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>{% endraw %}</pre>
In the final line, rewriting with two equations is
indicated by separating the two proofs of the relevant equations by a
vertical bar; the rewrite on the left is performed before that on the
right.


## Building proofs interactively

It is instructive to see how to build the alternative proof of
associativity using the interactive features of Agda in Emacs.
Begin by typing

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ m n p = ?

The question mark indicates that you would like Agda to help with
filling in that part of the code.  If you type `C-c C-l` (control-c
followed by control-l), the question mark will be replaced.

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ m n p = { }0

The empty braces are called a _hole_, and 0 is a number used for
referring to the hole.  The hole may display highlighted in green.
Emacs will also create a new window at the bottom of the screen
displaying the text

    ?0 : ((m + n) + p) ≡ (m + (n + p))

This indicates that hole 0 is to be filled in with a proof of
the stated judgement.

We wish to prove the proposition by induction on `m`.  Move
the cursor into the hole and type `C-c C-c`.  You will be given
the prompt:

    pattern variables to case (empty for split on result):

Typing `m` will cause a split on that variable, resulting
in an update to the code.

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = { }0
    +-assoc′ (suc m) n p = { }1

There are now two holes, and the window at the bottom tells you what
each is required to prove:

    ?0 : ((zero + n) + p) ≡ (zero + (n + p))
    ?1 : ((suc m + n) + p) ≡ (suc m + (n + p))

Going into hole 0 and typing `C-c C-,` will display the text:

    Goal: (n + p) ≡ (n + p)
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ

This indicates that after simplification the goal for hole 0 is as
stated, and that variables `p` and `n` of the stated types are
available to use in the proof.  The proof of the given goal is
trivial, and going into the goal and typing `C-c C-r` will fill it in,
renumbering the remaining hole to 0:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p = { }0

Going into the new hole 0 and typing `C-c C-,` will display the text:

    Goal: suc ((m + n) + p) ≡ suc (m + (n + p))
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ
    m : ℕ

Again, this gives the simplified goal and the available variables.
In this case, we need to rewrite by the induction
hypothesis, so let's edit the text accordingly:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p rewrite +-assoc′ m n p = { }0

Going into the remaining hole and typing `C-c C-,` will display the text:

    Goal: suc (m + (n + p)) ≡ suc (m + (n + p))
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ
    m : ℕ

The proof of the given goal is trivial, and going into the goal and
typing `C-c C-r` will fill it in, completing the proof:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p rewrite +-assoc′ m n p = refl


#### Exercise `+-swap` (recommended) {#plus-swap} 

Show

    m + (n + p) ≡ n + (m + p)

for all naturals `m`, `n`, and `p`. No induction is needed,
just apply the previous results which show addition
is associative and commutative.

#### Exercise `*-distrib-+` (recommended) {#times-distrib-plus}

Show multiplication distributes over addition, that is,

    (m + n) * p ≡ m * p + n * p

for all naturals `m`, `n`, and `p`.

#### Exercise `*-assoc` (recommended) {#times-assoc}

Show multiplication is associative, that is,

    (m * n) * p ≡ m * (n * p)

for all naturals `m`, `n`, and `p`.

#### Exercise `*-comm` {#times-comm}

Show multiplication is commutative, that is,

    m * n ≡ n * m

for all naturals `m` and `n`.  As with commutativity of addition,
you will need to formulate and prove suitable lemmas.

#### Exercise `0∸n≡0` {#zero-monus}

Show

    zero ∸ n ≡ zero

for all naturals `n`. Did your proof require induction?

#### Exercise `∸-+-assoc` {#monus-plus-assoc}

Show that monus associates with addition, that is,

    m ∸ n ∸ p ≡ m ∸ (n + p)

for all naturals `m`, `n`, and `p`.

#### Exercise `Bin-laws` (stretch) {#Bin-laws}

Recall that 
Exercise [Bin]({{ site.baseurl }}{% link out/plfa/Naturals.md%}#Bin)
defines a datatype of bitstrings representing natural numbers
<pre class="Agda">{% raw %}<a id="26362" class="Keyword">data</a> <a id="Bin"></a><a id="26367" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26367" class="Datatype">Bin</a> <a id="26371" class="Symbol">:</a> <a id="26373" class="PrimitiveType">Set</a> <a id="26377" class="Keyword">where</a>
  <a id="Bin.nil"></a><a id="26385" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26385" class="InductiveConstructor">nil</a> <a id="26389" class="Symbol">:</a> <a id="26391" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26367" class="Datatype">Bin</a>
  <a id="Bin.x0_"></a><a id="26397" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26397" class="InductiveConstructor Operator">x0_</a> <a id="26401" class="Symbol">:</a> <a id="26403" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26367" class="Datatype">Bin</a> <a id="26407" class="Symbol">→</a> <a id="26409" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26367" class="Datatype">Bin</a>
  <a id="Bin.x1_"></a><a id="26415" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26415" class="InductiveConstructor Operator">x1_</a> <a id="26419" class="Symbol">:</a> <a id="26421" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26367" class="Datatype">Bin</a> <a id="26425" class="Symbol">→</a> <a id="26427" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Induction.md %}{% raw %}#26367" class="Datatype">Bin</a>{% endraw %}</pre>
and asks you to define functions

    inc   : Bin → Bin
    to    : ℕ → Bin
    from  : Bin → ℕ

Consider the following laws, where `n` ranges over naturals and `x`
over bitstrings.

    from (inc x) ≡ suc (from x)
    to (from x) ≡ x
    from (to n) ≡ n

For each law: if it holds, prove; if not, give a counterexample.


## Standard library

Definitions similar to those in this chapter can be found in the standard library.
<pre class="Agda">{% raw %}<a id="26882" class="Keyword">import</a> <a id="26889" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="26909" class="Keyword">using</a> <a id="26915" class="Symbol">(</a><a id="26916" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7678" class="Function">+-assoc</a><a id="26923" class="Symbol">;</a> <a id="26925" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7834" class="Function">+-identityʳ</a><a id="26936" class="Symbol">;</a> <a id="26938" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7575" class="Function">+-suc</a><a id="26943" class="Symbol">;</a> <a id="26945" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a><a id="26951" class="Symbol">)</a>{% endraw %}</pre>

## Unicode

This chapter uses the following unicode.

    ∀  U+2200  FOR ALL (\forall)
    ʳ  U+02B3  MODIFIER LETTER SMALL R (\^r)
    ′  U+2032  PRIME (\')
    ″  U+2033  DOUBLE PRIME (\')
    ‴  U+2034  TRIPLE PRIME (\')
    ⁗  U+2057  QUADRUPLE PRIME (\')

Similar to `\r`, the command `\^r` gives access to a variety of
superscript rightward arrows, and also a superscript letter `r`.
The command `\'` gives access to a range of primes (`′ ″ ‴ ⁗`).
