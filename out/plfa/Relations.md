---
src       : src/plfa/Relations.lagda
title     : "Relations: Inductive definition of relations"
layout    : page
prev      : /Induction/
permalink : /Relations/
next      : /Equality/
---

<pre class="Agda">{% raw %}<a id="170" class="Keyword">module</a> <a id="177" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}" class="Module">plfa.Relations</a> <a id="192" class="Keyword">where</a>{% endraw %}</pre>

After having defined operations such as addition and multiplication,
the next step is to define relations, such as _less than or equal_.

## Imports

<pre class="Agda">{% raw %}<a id="373" class="Keyword">import</a> <a id="380" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Relation.Binary.PropositionalEquality</a> <a id="418" class="Symbol">as</a> <a id="421" class="Module">Eq</a>
<a id="424" class="Keyword">open</a> <a id="429" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html" class="Module">Eq</a> <a id="432" class="Keyword">using</a> <a id="438" class="Symbol">(</a><a id="439" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">_≡_</a><a id="442" class="Symbol">;</a> <a id="444" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a><a id="448" class="Symbol">;</a> <a id="450" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a><a id="454" class="Symbol">;</a> <a id="456" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.Core.html#838" class="Function">sym</a><a id="459" class="Symbol">)</a>
<a id="461" class="Keyword">open</a> <a id="466" class="Keyword">import</a> <a id="473" href="https://agda.github.io/agda-stdlib/Data.Nat.html" class="Module">Data.Nat</a> <a id="482" class="Keyword">using</a> <a id="488" class="Symbol">(</a><a id="489" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="490" class="Symbol">;</a> <a id="492" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="496" class="Symbol">;</a> <a id="498" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a><a id="501" class="Symbol">;</a> <a id="503" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">_+_</a><a id="506" class="Symbol">;</a> <a id="508" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#433" class="Primitive Operator">_*_</a><a id="511" class="Symbol">;</a> <a id="513" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#320" class="Primitive Operator">_∸_</a><a id="516" class="Symbol">)</a>
<a id="518" class="Keyword">open</a> <a id="523" class="Keyword">import</a> <a id="530" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="550" class="Keyword">using</a> <a id="556" class="Symbol">(</a><a id="557" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a><a id="563" class="Symbol">;</a> <a id="565" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#7575" class="Function">+-suc</a><a id="570" class="Symbol">)</a>
<a id="572" class="Keyword">open</a> <a id="577" class="Keyword">import</a> <a id="584" href="https://agda.github.io/agda-stdlib/Data.List.html" class="Module">Data.List</a> <a id="594" class="Keyword">using</a> <a id="600" class="Symbol">(</a><a id="601" href="https://agda.github.io/agda-stdlib/Agda.Builtin.List.html#80" class="Datatype">List</a><a id="605" class="Symbol">;</a> <a id="607" href="https://agda.github.io/agda-stdlib/Data.List.Base.html#8019" class="InductiveConstructor">[]</a><a id="609" class="Symbol">;</a> <a id="611" href="https://agda.github.io/agda-stdlib/Agda.Builtin.List.html#132" class="InductiveConstructor Operator">_∷_</a><a id="614" class="Symbol">)</a>
<a id="616" class="Keyword">open</a> <a id="621" class="Keyword">import</a> <a id="628" href="https://agda.github.io/agda-stdlib/Function.html" class="Module">Function</a> <a id="637" class="Keyword">using</a> <a id="643" class="Symbol">(</a><a id="644" href="https://agda.github.io/agda-stdlib/Function.html#1068" class="Function">id</a><a id="646" class="Symbol">;</a> <a id="648" href="https://agda.github.io/agda-stdlib/Function.html#769" class="Function Operator">_∘_</a><a id="651" class="Symbol">)</a>
<a id="653" class="Keyword">open</a> <a id="658" class="Keyword">import</a> <a id="665" href="https://agda.github.io/agda-stdlib/Relation.Nullary.html" class="Module">Relation.Nullary</a> <a id="682" class="Keyword">using</a> <a id="688" class="Symbol">(</a><a id="689" href="https://agda.github.io/agda-stdlib/Relation.Nullary.html#464" class="Function Operator">¬_</a><a id="691" class="Symbol">)</a>
<a id="693" class="Keyword">open</a> <a id="698" class="Keyword">import</a> <a id="705" href="https://agda.github.io/agda-stdlib/Data.Empty.html" class="Module">Data.Empty</a> <a id="716" class="Keyword">using</a> <a id="722" class="Symbol">(</a><a id="723" href="https://agda.github.io/agda-stdlib/Data.Empty.html#243" class="Datatype">⊥</a><a id="724" class="Symbol">;</a> <a id="726" href="https://agda.github.io/agda-stdlib/Data.Empty.html#360" class="Function">⊥-elim</a><a id="732" class="Symbol">)</a>{% endraw %}</pre>


## Defining relations

The relation _less than or equal_ has an infinite number of
instances.  Here are a few of them:

    0 ≤ 0     0 ≤ 1     0 ≤ 2     0 ≤ 3     ...
              1 ≤ 1     1 ≤ 2     1 ≤ 3     ...
                        2 ≤ 2     2 ≤ 3     ...
                                  3 ≤ 3     ...
                                            ...

And yet, we can write a finite definition that encompasses
all of these instances in just a few lines.  Here is the
definition as a pair of inference rules:

    z≤n --------
        zero ≤ n

        m ≤ n
    s≤s -------------
        suc m ≤ suc n

And here is the definition in Agda:
<pre class="Agda">{% raw %}<a id="1409" class="Keyword">data</a> <a id="_≤_"></a><a id="1414" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">_≤_</a> <a id="1418" class="Symbol">:</a> <a id="1420" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="1422" class="Symbol">→</a> <a id="1424" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="1426" class="Symbol">→</a> <a id="1428" class="PrimitiveType">Set</a> <a id="1432" class="Keyword">where</a>

  <a id="_≤_.z≤n"></a><a id="1441" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a> <a id="1445" class="Symbol">:</a> <a id="1447" class="Symbol">∀</a> <a id="1449" class="Symbol">{</a><a id="1450" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1450" class="Bound">n</a> <a id="1452" class="Symbol">:</a> <a id="1454" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="1455" class="Symbol">}</a>
      <a id="1463" class="Comment">--------</a>
    <a id="1476" class="Symbol">→</a> <a id="1478" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="1483" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="1485" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1450" class="Bound">n</a>

  <a id="_≤_.s≤s"></a><a id="1490" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="1494" class="Symbol">:</a> <a id="1496" class="Symbol">∀</a> <a id="1498" class="Symbol">{</a><a id="1499" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1499" class="Bound">m</a> <a id="1501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1501" class="Bound">n</a> <a id="1503" class="Symbol">:</a> <a id="1505" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="1506" class="Symbol">}</a>
    <a id="1512" class="Symbol">→</a> <a id="1514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1499" class="Bound">m</a> <a id="1516" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="1518" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1501" class="Bound">n</a>
      <a id="1526" class="Comment">-------------</a>
    <a id="1544" class="Symbol">→</a> <a id="1546" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="1550" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1499" class="Bound">m</a> <a id="1552" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="1554" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="1558" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1501" class="Bound">n</a>{% endraw %}</pre>
Here `z≤n` and `s≤s` (with no spaces) are constructor names, while
`zero ≤ m`, and `m ≤ n` and `suc m ≤ suc n` (with spaces) are types.
This is our first use of an _indexed_ datatype, where the type `m ≤ n`
is indexed by two naturals, `m` and `n`.  In Agda any line beginning
with two or more dashes is a comment, and here we have exploited that
convention to write our Agda code in a form that resembles the
corresponding inference rules, a trick we will use often from now on.

Both definitions above tell us the same two things:

* _Base case_: for all naturals `n`, the proposition `zero ≤ n` holds
* _Inductive case_: for all naturals `m` and `n`, if the proposition
  `m ≤ n` holds, then the proposition `suc m ≤ suc n` holds.

In fact, they each give us a bit more detail:

* _Base case_: for all naturals `n`, the constructor `z≤n`
  produces evidence that `zero ≤ n` holds.
* _Inductive case_: for all naturals `m` and `n`, the constructor
  `s≤s` takes evidence that `m ≤ n` holds into evidence that
  `suc m ≤ suc n` holds.

For example, here in inference rule notation is the proof that
`2 ≤ 4`.

      z≤n -----
          0 ≤ 2
     s≤s -------
          1 ≤ 3
    s≤s ---------
          2 ≤ 4

And here is the corresponding Agda proof.
<pre class="Agda">{% raw %}<a id="2835" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#2835" class="Function">_</a> <a id="2837" class="Symbol">:</a> <a id="2839" class="Number">2</a> <a id="2841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="2843" class="Number">4</a>
<a id="2845" class="Symbol">_</a> <a id="2847" class="Symbol">=</a> <a id="2849" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="2853" class="Symbol">(</a><a id="2854" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="2858" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a><a id="2861" class="Symbol">)</a>{% endraw %}</pre>




## Implicit arguments

This is our first use of implicit arguments.  In the definition of
inequality, the two lines defining the constructors use `∀`, very
similar to our use of `∀` in propositions such as:

    +-comm : ∀ (m n : ℕ) → m + n ≡ n + m

However, here the declarations are surrounded by curly braces `{ }`
rather than parentheses `( )`.  This means that the arguments are
_implicit_ and need not be written explicitly; instead, they are
_inferred_ by Agda's typechecker. Thus, we write `+-comm m n` for the
proof that `m + n ≡ n + m`, but `z≤n` for the proof that `zero ≤ n`,
leaving `n` implicit.  Similarly, if `m≤n` is evidence that `m ≤ n`,
we write `s≤s m≤n` for evidence that `suc m ≤ suc n`, leaving both `m`
and `n` implicit.

If we wish, it is possible to provide implicit arguments explicitly by
writing the arguments inside curly braces.  For instance, here is the
Agda proof that `2 ≤ 4` repeated, with the implicit arguments made
explicit.
<pre class="Agda">{% raw %}<a id="3856" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#3856" class="Function">_</a> <a id="3858" class="Symbol">:</a> <a id="3860" class="Number">2</a> <a id="3862" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="3864" class="Number">4</a>
<a id="3866" class="Symbol">_</a> <a id="3868" class="Symbol">=</a> <a id="3870" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="3874" class="Symbol">{</a><a id="3875" class="Number">1</a><a id="3876" class="Symbol">}</a> <a id="3878" class="Symbol">{</a><a id="3879" class="Number">3</a><a id="3880" class="Symbol">}</a> <a id="3882" class="Symbol">(</a><a id="3883" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="3887" class="Symbol">{</a><a id="3888" class="Number">0</a><a id="3889" class="Symbol">}</a> <a id="3891" class="Symbol">{</a><a id="3892" class="Number">2</a><a id="3893" class="Symbol">}</a> <a id="3895" class="Symbol">(</a><a id="3896" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a> <a id="3900" class="Symbol">{</a><a id="3901" class="Number">2</a><a id="3902" class="Symbol">}))</a>{% endraw %}</pre>
One may also identify implicit arguments by name.
<pre class="Agda">{% raw %}<a id="3980" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#3980" class="Function">_</a> <a id="3982" class="Symbol">:</a> <a id="3984" class="Number">2</a> <a id="3986" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="3988" class="Number">4</a>
<a id="3990" class="Symbol">_</a> <a id="3992" class="Symbol">=</a> <a id="3994" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="3998" class="Symbol">{</a><a id="3999" class="Argument">m</a> <a id="4001" class="Symbol">=</a> <a id="4003" class="Number">1</a><a id="4004" class="Symbol">}</a> <a id="4006" class="Symbol">{</a><a id="4007" class="Argument">n</a> <a id="4009" class="Symbol">=</a> <a id="4011" class="Number">3</a><a id="4012" class="Symbol">}</a> <a id="4014" class="Symbol">(</a><a id="4015" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="4019" class="Symbol">{</a><a id="4020" class="Argument">m</a> <a id="4022" class="Symbol">=</a> <a id="4024" class="Number">0</a><a id="4025" class="Symbol">}</a> <a id="4027" class="Symbol">{</a><a id="4028" class="Argument">n</a> <a id="4030" class="Symbol">=</a> <a id="4032" class="Number">2</a><a id="4033" class="Symbol">}</a> <a id="4035" class="Symbol">(</a><a id="4036" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a> <a id="4040" class="Symbol">{</a><a id="4041" class="Argument">n</a> <a id="4043" class="Symbol">=</a> <a id="4045" class="Number">2</a><a id="4046" class="Symbol">}))</a>{% endraw %}</pre>
In the latter format, you may only supply some implicit arguments.
<pre class="Agda">{% raw %}<a id="4141" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#4141" class="Function">_</a> <a id="4143" class="Symbol">:</a> <a id="4145" class="Number">2</a> <a id="4147" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="4149" class="Number">4</a>
<a id="4151" class="Symbol">_</a> <a id="4153" class="Symbol">=</a> <a id="4155" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="4159" class="Symbol">{</a><a id="4160" class="Argument">n</a> <a id="4162" class="Symbol">=</a> <a id="4164" class="Number">3</a><a id="4165" class="Symbol">}</a> <a id="4167" class="Symbol">(</a><a id="4168" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="4172" class="Symbol">{</a><a id="4173" class="Argument">n</a> <a id="4175" class="Symbol">=</a> <a id="4177" class="Number">2</a><a id="4178" class="Symbol">}</a> <a id="4180" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a><a id="4183" class="Symbol">)</a>{% endraw %}</pre>
It is not permitted to swap implicit arguments, even when named.


## Precedence

We declare the precedence for comparison as follows.
<pre class="Agda">{% raw %}<a id="4344" class="Keyword">infix</a> <a id="4350" class="Number">4</a> <a id="4352" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">_≤_</a>{% endraw %}</pre>
We set the precedence of `_≤_` at level 4, so it binds less tightly
that `_+_` at level 6 and hence `1 + 2 ≤ 3` parses as `(1 + 2) ≤ 3`.
We write `infix` to indicate that the operator does not associate to
either the left or right, as it makes no sense to parse `1 ≤ 2 ≤ 3` as
either `(1 ≤ 2) ≤ 3` or `1 ≤ (2 ≤ 3)`.


## Decidability

Given two numbers, it is straightforward to compute whether or not the
first is less than or equal to the second.  We don't give the code for
doing so here, but will return to this point in
Chapter [Decidable]({{ site.baseurl }}{% link out/plfa/Decidable.md%}).


## Properties of ordering relations

Relations pop up all the time, and mathematicians have agreed
on names for some of the most common properties.

* _Reflexive_ For all `n`, the relation `n ≤ n` holds.
* _Transitive_ For all `m`, `n`, and `p`, if `m ≤ n` and
`n ≤ p` hold, then `m ≤ p` holds.
* _Anti-symmetric_ For all `m` and `n`, if both `m ≤ n` and
`n ≤ m` hold, then `m ≡ n` holds.
* _Total_ For all `m` and `n`, either `m ≤ n` or `n ≤ m`
holds.

The relation `_≤_` satisfies all four of these properties.

There are also names for some combinations of these properties.

* _Preorder_ Any relation that is reflexive and transitive.
* _Partial order_ Any preorder that is also anti-symmetric.
* _Total order_ Any partial order that is also total.

If you ever bump into a relation at a party, you now know how
to make small talk, by asking it whether it is reflexive, transitive,
anti-symmetric, and total. Or instead you might ask whether it is a
preorder, partial order, or total order.

Less frivolously, if you ever bump into a relation while reading a
technical paper, this gives you a way to orient yourself, by checking
whether or not it is a preorder, partial order, or total order.  A
careful author will often call out these properties---or their
lack---for instance by saying that a newly introduced relation is a
a partial order but not a total order.


#### Exercise `orderings` {#orderings}

Give an example of a preorder that is not a partial order.

Give an example of a partial order that is not a preorder.


## Reflexivity

The first property to prove about comparison is that it is reflexive:
for any natural `n`, the relation `n ≤ n` holds.  We follow the
convention in the standard library and make the argument implicit,
as that will make it easier to invoke reflection.
<pre class="Agda">{% raw %}<a id="≤-refl"></a><a id="6744" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6744" class="Function">≤-refl</a> <a id="6751" class="Symbol">:</a> <a id="6753" class="Symbol">∀</a> <a id="6755" class="Symbol">{</a><a id="6756" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6756" class="Bound">n</a> <a id="6758" class="Symbol">:</a> <a id="6760" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="6761" class="Symbol">}</a>
    <a id="6767" class="Comment">-----</a>
  <a id="6775" class="Symbol">→</a> <a id="6777" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6756" class="Bound">n</a> <a id="6779" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="6781" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6756" class="Bound">n</a>
<a id="6783" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6744" class="Function">≤-refl</a> <a id="6790" class="Symbol">{</a><a id="6791" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a><a id="6795" class="Symbol">}</a>   <a id="6799" class="Symbol">=</a>  <a id="6802" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="6806" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6744" class="Function">≤-refl</a> <a id="6813" class="Symbol">{</a><a id="6814" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="6818" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6818" class="Bound">n</a><a id="6819" class="Symbol">}</a>  <a id="6822" class="Symbol">=</a>  <a id="6825" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="6829" class="Symbol">(</a><a id="6830" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6744" class="Function">≤-refl</a> <a id="6837" class="Symbol">{</a><a id="6838" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#6818" class="Bound">n</a><a id="6839" class="Symbol">})</a>{% endraw %}</pre>
The proof is a straightforward induction on the implicit argument `n`.
In the base case, `zero ≤ zero` holds by `z≤n`.  In the inductive
case, the inductive hypothesis `≤-refl {n}` gives us a proof of `n ≤
n`, and applying `s≤s` to that yields a proof of `suc n ≤ suc n`.

It is a good exercise to prove reflexivity interactively in Emacs,
using holes and the `C-c C-c`, `C-c C-,`, and `C-c C-r` commands.


## Transitivity

The second property to prove about comparison is that it is
transitive: for any naturals `m`, `n`, and `p`, if `m ≤ n` and `n ≤ p`
hold, then `m ≤ p` holds.  Again, `m`, `n`, and `p` are implicit.
<pre class="Agda">{% raw %}<a id="≤-trans"></a><a id="7488" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7488" class="Function">≤-trans</a> <a id="7496" class="Symbol">:</a> <a id="7498" class="Symbol">∀</a> <a id="7500" class="Symbol">{</a><a id="7501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7501" class="Bound">m</a> <a id="7503" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7503" class="Bound">n</a> <a id="7505" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7505" class="Bound">p</a> <a id="7507" class="Symbol">:</a> <a id="7509" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="7510" class="Symbol">}</a>
  <a id="7514" class="Symbol">→</a> <a id="7516" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7501" class="Bound">m</a> <a id="7518" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="7520" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7503" class="Bound">n</a>
  <a id="7524" class="Symbol">→</a> <a id="7526" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7503" class="Bound">n</a> <a id="7528" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="7530" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7505" class="Bound">p</a>
    <a id="7536" class="Comment">-----</a>
  <a id="7544" class="Symbol">→</a> <a id="7546" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7501" class="Bound">m</a> <a id="7548" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="7550" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7505" class="Bound">p</a>
<a id="7552" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7488" class="Function">≤-trans</a> <a id="7560" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>       <a id="7570" class="Symbol">_</a>          <a id="7581" class="Symbol">=</a>  <a id="7584" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="7588" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7488" class="Function">≤-trans</a> <a id="7596" class="Symbol">(</a><a id="7597" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="7601" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7601" class="Bound">m≤n</a><a id="7604" class="Symbol">)</a> <a id="7606" class="Symbol">(</a><a id="7607" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="7611" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7611" class="Bound">n≤p</a><a id="7614" class="Symbol">)</a>  <a id="7617" class="Symbol">=</a>  <a id="7620" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="7624" class="Symbol">(</a><a id="7625" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7488" class="Function">≤-trans</a> <a id="7633" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7601" class="Bound">m≤n</a> <a id="7637" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7611" class="Bound">n≤p</a><a id="7640" class="Symbol">)</a>{% endraw %}</pre>
Here the proof is by induction on the _evidence_ that `m ≤ n`.  In the
base case, the first inequality holds by `z≤n` and must show `zero ≤
p`, which follows immediately by `z≤n`.  In this case, the fact that
`n ≤ p` is irrelevant, and we write `_` as the pattern to indicate
that the corresponding evidence is unused.

In the inductive case, the first inequality holds by `s≤s m≤n`
and the second inequality by `s≤s n≤p`, and so we are given
`suc m ≤ suc n` and `suc n ≤ suc p`, and must show `suc m ≤ suc p`.
The inductive hypothesis `≤-trans m≤n n≤p` establishes
that `m ≤ p`, and our goal follows by applying `s≤s`.

The case `≤-trans (s≤s m≤n) z≤n` cannot arise, since the first
inequality implies the middle value is `suc n` while the second
inequality implies that it is `zero`.  Agda can determine that such a
case cannot arise, and does not require (or permit) it to be listed.

Alternatively, we could make the implicit parameters explicit.
<pre class="Agda">{% raw %}<a id="≤-trans′"></a><a id="8617" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8617" class="Function">≤-trans′</a> <a id="8626" class="Symbol">:</a> <a id="8628" class="Symbol">∀</a> <a id="8630" class="Symbol">(</a><a id="8631" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8631" class="Bound">m</a> <a id="8633" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8633" class="Bound">n</a> <a id="8635" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8635" class="Bound">p</a> <a id="8637" class="Symbol">:</a> <a id="8639" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="8640" class="Symbol">)</a>
  <a id="8644" class="Symbol">→</a> <a id="8646" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8631" class="Bound">m</a> <a id="8648" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="8650" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8633" class="Bound">n</a>
  <a id="8654" class="Symbol">→</a> <a id="8656" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8633" class="Bound">n</a> <a id="8658" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="8660" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8635" class="Bound">p</a>
    <a id="8666" class="Comment">-----</a>
  <a id="8674" class="Symbol">→</a> <a id="8676" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8631" class="Bound">m</a> <a id="8678" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="8680" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8635" class="Bound">p</a>
<a id="8682" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8617" class="Function">≤-trans′</a> <a id="8691" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="8699" class="Symbol">_</a>       <a id="8707" class="Symbol">_</a>       <a id="8715" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>       <a id="8725" class="Symbol">_</a>          <a id="8736" class="Symbol">=</a>  <a id="8739" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="8743" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8617" class="Function">≤-trans′</a> <a id="8752" class="Symbol">(</a><a id="8753" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8757" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8757" class="Bound">m</a><a id="8758" class="Symbol">)</a> <a id="8760" class="Symbol">(</a><a id="8761" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8765" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8765" class="Bound">n</a><a id="8766" class="Symbol">)</a> <a id="8768" class="Symbol">(</a><a id="8769" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="8773" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8773" class="Bound">p</a><a id="8774" class="Symbol">)</a> <a id="8776" class="Symbol">(</a><a id="8777" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="8781" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8781" class="Bound">m≤n</a><a id="8784" class="Symbol">)</a> <a id="8786" class="Symbol">(</a><a id="8787" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="8791" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8791" class="Bound">n≤p</a><a id="8794" class="Symbol">)</a>  <a id="8797" class="Symbol">=</a>  <a id="8800" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="8804" class="Symbol">(</a><a id="8805" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8617" class="Function">≤-trans′</a> <a id="8814" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8757" class="Bound">m</a> <a id="8816" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8765" class="Bound">n</a> <a id="8818" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8773" class="Bound">p</a> <a id="8820" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8781" class="Bound">m≤n</a> <a id="8824" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#8791" class="Bound">n≤p</a><a id="8827" class="Symbol">)</a>{% endraw %}</pre>
One might argue that this is clearer or one might argue that the extra
length obscures the essence of the proof.  We will usually opt for
shorter proofs.

The technique of induction on evidence that a property holds (e.g.,
inducting on evidence that `m ≤ n`)---rather than induction on 
values of which the property holds (e.g., inducting on `m`)---will turn
out to be immensely valuable, and one that we use often.

Again, it is a good exercise to prove transitivity interactively in Emacs,
using holes and the `C-c C-c`, `C-c C-,`, and `C-c C-r` commands.


## Anti-symmetry

The third property to prove about comparison is that it is
antisymmetric: for all naturals `m` and `n`, if both `m ≤ n` and `n ≤
m` hold, then `m ≡ n` holds.
<pre class="Agda">{% raw %}<a id="≤-antisym"></a><a id="9589" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9589" class="Function">≤-antisym</a> <a id="9599" class="Symbol">:</a> <a id="9601" class="Symbol">∀</a> <a id="9603" class="Symbol">{</a><a id="9604" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9604" class="Bound">m</a> <a id="9606" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9606" class="Bound">n</a> <a id="9608" class="Symbol">:</a> <a id="9610" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="9611" class="Symbol">}</a>
  <a id="9615" class="Symbol">→</a> <a id="9617" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9604" class="Bound">m</a> <a id="9619" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="9621" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9606" class="Bound">n</a>
  <a id="9625" class="Symbol">→</a> <a id="9627" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9606" class="Bound">n</a> <a id="9629" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="9631" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9604" class="Bound">m</a>
    <a id="9637" class="Comment">-----</a>
  <a id="9645" class="Symbol">→</a> <a id="9647" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9604" class="Bound">m</a> <a id="9649" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#83" class="Datatype Operator">≡</a> <a id="9651" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9606" class="Bound">n</a>
<a id="9653" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9589" class="Function">≤-antisym</a> <a id="9663" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>       <a id="9673" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>        <a id="9684" class="Symbol">=</a>  <a id="9687" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Equality.html#140" class="InductiveConstructor">refl</a>
<a id="9692" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9589" class="Function">≤-antisym</a> <a id="9702" class="Symbol">(</a><a id="9703" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="9707" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9707" class="Bound">m≤n</a><a id="9710" class="Symbol">)</a> <a id="9712" class="Symbol">(</a><a id="9713" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="9717" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9717" class="Bound">n≤m</a><a id="9720" class="Symbol">)</a>  <a id="9723" class="Symbol">=</a>  <a id="9726" href="https://agda.github.io/agda-stdlib/Relation.Binary.PropositionalEquality.html#1056" class="Function">cong</a> <a id="9731" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="9735" class="Symbol">(</a><a id="9736" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9589" class="Function">≤-antisym</a> <a id="9746" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9707" class="Bound">m≤n</a> <a id="9750" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#9717" class="Bound">n≤m</a><a id="9753" class="Symbol">)</a>{% endraw %}</pre>
Again, the proof is by induction over the evidence that `m ≤ n`
and `n ≤ m` hold.

In the base case, both inequalities hold by `z≤n`, and so we are given
`zero ≤ zero` and `zero ≤ zero` and must show `zero ≡ zero`, which
follows by reflexivity.  (Reflexivity of equality, that is, not
reflexivity of inequality.)

In the inductive case, the first inequality holds by `s≤s m≤n` and the
second inequality holds by `s≤s n≤m`, and so we are given `suc m ≤ suc n`
and `suc n ≤ suc m` and must show `suc m ≡ suc n`.  The inductive
hypothesis `≤-antisym m≤n n≤m` establishes that `m ≡ n`, and our goal
follows by congruence.

#### Exercise `≤-antisym-cases` {#leq-antisym-cases} 

The above proof omits cases where one argument is `z≤n` and one
argument is `s≤s`.  Why is it ok to omit them?


## Total

The fourth property to prove about comparison is that it is total:
for any naturals `m` and `n` either `m ≤ n` or `n ≤ m`, or both if
`m` and `n` are equal.

We specify what it means for inequality to be total.
<pre class="Agda">{% raw %}<a id="10787" class="Keyword">data</a> <a id="Total"></a><a id="10792" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="10798" class="Symbol">(</a><a id="10799" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10799" class="Bound">m</a> <a id="10801" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10801" class="Bound">n</a> <a id="10803" class="Symbol">:</a> <a id="10805" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="10806" class="Symbol">)</a> <a id="10808" class="Symbol">:</a> <a id="10810" class="PrimitiveType">Set</a> <a id="10814" class="Keyword">where</a>

  <a id="Total.forward"></a><a id="10823" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="10831" class="Symbol">:</a>
      <a id="10839" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10799" class="Bound">m</a> <a id="10841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="10843" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10801" class="Bound">n</a>
      <a id="10851" class="Comment">---------</a>
    <a id="10865" class="Symbol">→</a> <a id="10867" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="10873" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10799" class="Bound">m</a> <a id="10875" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10801" class="Bound">n</a>

  <a id="Total.flipped"></a><a id="10880" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="10888" class="Symbol">:</a>
      <a id="10896" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10801" class="Bound">n</a> <a id="10898" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="10900" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10799" class="Bound">m</a>
      <a id="10908" class="Comment">---------</a>
    <a id="10922" class="Symbol">→</a> <a id="10924" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="10930" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10799" class="Bound">m</a> <a id="10932" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10801" class="Bound">n</a>{% endraw %}</pre>
Evidence that `Total m n` holds is either of the form
`forward m≤n` or `flipped n≤m`, where `m≤n` and `n≤m` are
evidence of `m ≤ n` and `n ≤ m` respectively.

This is our first use of a datatype with _parameters_,
in this case `m` and `n`.  It is equivalent to the following
indexed datatype.
<pre class="Agda">{% raw %}<a id="11251" class="Keyword">data</a> <a id="Total′"></a><a id="11256" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11256" class="Datatype">Total′</a> <a id="11263" class="Symbol">:</a> <a id="11265" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="11267" class="Symbol">→</a> <a id="11269" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="11271" class="Symbol">→</a> <a id="11273" class="PrimitiveType">Set</a> <a id="11277" class="Keyword">where</a>

  <a id="Total′.forward′"></a><a id="11286" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11286" class="InductiveConstructor">forward′</a> <a id="11295" class="Symbol">:</a> <a id="11297" class="Symbol">∀</a> <a id="11299" class="Symbol">{</a><a id="11300" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11300" class="Bound">m</a> <a id="11302" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11302" class="Bound">n</a> <a id="11304" class="Symbol">:</a> <a id="11306" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11307" class="Symbol">}</a>
    <a id="11313" class="Symbol">→</a> <a id="11315" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11300" class="Bound">m</a> <a id="11317" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="11319" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11302" class="Bound">n</a>
      <a id="11327" class="Comment">----------</a>
    <a id="11342" class="Symbol">→</a> <a id="11344" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11256" class="Datatype">Total′</a> <a id="11351" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11300" class="Bound">m</a> <a id="11353" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11302" class="Bound">n</a>

  <a id="Total′.flipped′"></a><a id="11358" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11358" class="InductiveConstructor">flipped′</a> <a id="11367" class="Symbol">:</a> <a id="11369" class="Symbol">∀</a> <a id="11371" class="Symbol">{</a><a id="11372" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11372" class="Bound">m</a> <a id="11374" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11374" class="Bound">n</a> <a id="11376" class="Symbol">:</a> <a id="11378" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11379" class="Symbol">}</a>
    <a id="11385" class="Symbol">→</a> <a id="11387" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11374" class="Bound">n</a> <a id="11389" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="11391" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11372" class="Bound">m</a>
      <a id="11399" class="Comment">----------</a>
    <a id="11414" class="Symbol">→</a> <a id="11416" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11256" class="Datatype">Total′</a> <a id="11423" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11372" class="Bound">m</a> <a id="11425" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11374" class="Bound">n</a>{% endraw %}</pre>
Each parameter of the type translates as an implicit parameter of each
constructor.  Unlike an indexed datatype, where the indexes can vary
(as in `zero ≤ n` and `suc m ≤ suc n`), in a parameterised datatype
the parameters must always be the same (as in `Total m n`).
Parameterised declarations are shorter, easier to read, and
occcasionally aid Agda's termination checker, so we will use them in
preference to indexed types when possible.

With that preliminary out of the way, we specify and prove totality.
<pre class="Agda">{% raw %}<a id="≤-total"></a><a id="11961" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11961" class="Function">≤-total</a> <a id="11969" class="Symbol">:</a> <a id="11971" class="Symbol">∀</a> <a id="11973" class="Symbol">(</a><a id="11974" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11974" class="Bound">m</a> <a id="11976" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11976" class="Bound">n</a> <a id="11978" class="Symbol">:</a> <a id="11980" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="11981" class="Symbol">)</a> <a id="11983" class="Symbol">→</a> <a id="11985" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="11991" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11974" class="Bound">m</a> <a id="11993" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11976" class="Bound">n</a>
<a id="11995" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11961" class="Function">≤-total</a> <a id="12003" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="12011" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12011" class="Bound">n</a>                         <a id="12037" class="Symbol">=</a>  <a id="12040" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="12048" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="12052" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11961" class="Function">≤-total</a> <a id="12060" class="Symbol">(</a><a id="12061" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="12065" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12065" class="Bound">m</a><a id="12066" class="Symbol">)</a> <a id="12068" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>                      <a id="12094" class="Symbol">=</a>  <a id="12097" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="12105" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="12109" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11961" class="Function">≤-total</a> <a id="12117" class="Symbol">(</a><a id="12118" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="12122" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12122" class="Bound">m</a><a id="12123" class="Symbol">)</a> <a id="12125" class="Symbol">(</a><a id="12126" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="12130" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12130" class="Bound">n</a><a id="12131" class="Symbol">)</a> <a id="12133" class="Keyword">with</a> <a id="12138" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#11961" class="Function">≤-total</a> <a id="12146" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12122" class="Bound">m</a> <a id="12148" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12130" class="Bound">n</a>
<a id="12150" class="Symbol">...</a>                        <a id="12177" class="Symbol">|</a> <a id="12179" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="12187" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12187" class="Bound">m≤n</a>  <a id="12192" class="Symbol">=</a>  <a id="12195" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="12203" class="Symbol">(</a><a id="12204" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="12208" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12187" class="Bound">m≤n</a><a id="12211" class="Symbol">)</a>
<a id="12213" class="Symbol">...</a>                        <a id="12240" class="Symbol">|</a> <a id="12242" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="12250" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12250" class="Bound">n≤m</a>  <a id="12255" class="Symbol">=</a>  <a id="12258" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="12266" class="Symbol">(</a><a id="12267" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="12271" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#12250" class="Bound">n≤m</a><a id="12274" class="Symbol">)</a>{% endraw %}</pre>
In this case the proof is by induction over both the first
and second arguments.  We perform a case analysis:

* _First base case_: If the first argument is `zero` and the
  second argument is `n` then the forward case holds,
  with `z≤n` as evidence that `zero ≤ n`.

* _Second base case_: If the first argument is `suc m` and the
  second argument is `zero` then the flipped case holds, with
  `z≤n` as evidence that `zero ≤ suc m`.

* _Inductive case_: If the first argument is `suc m` and the
  second argument is `suc n`, then the inductive hypothesis
  `≤-total m n` establishes one of the following:

  + The forward case of the inductive hypothesis holds with `m≤n` as
    evidence that `m ≤ n`, from which it follows that the forward case of the
    proposition holds with `s≤s m≤n` as evidence that `suc m ≤ suc n`.

  + The flipped case of the inductive hypothesis holds with `n≤m` as
    evidence that `n ≤ m`, from which it follows that the flipped case of the
    proposition holds with `s≤s n≤m` as evidence that `suc n ≤ suc m`.

This is our first use of the `with` clause in Agda.  The keyword
`with` is followed by an expression and one or more subsequent lines.
Each line begins with an ellipsis (`...`) and a vertical bar (`|`),
followed by a pattern to be matched against the expression
and the right-hand side of the equation.

Every use of `with` is equivalent to defining a helper function.  For
example, the definition above is equivalent to the following.
<pre class="Agda">{% raw %}<a id="≤-total′"></a><a id="13782" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13782" class="Function">≤-total′</a> <a id="13791" class="Symbol">:</a> <a id="13793" class="Symbol">∀</a> <a id="13795" class="Symbol">(</a><a id="13796" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13796" class="Bound">m</a> <a id="13798" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13798" class="Bound">n</a> <a id="13800" class="Symbol">:</a> <a id="13802" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="13803" class="Symbol">)</a> <a id="13805" class="Symbol">→</a> <a id="13807" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="13813" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13796" class="Bound">m</a> <a id="13815" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13798" class="Bound">n</a>
<a id="13817" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13782" class="Function">≤-total′</a> <a id="13826" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="13834" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13834" class="Bound">n</a>        <a id="13843" class="Symbol">=</a>  <a id="13846" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="13854" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="13858" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13782" class="Function">≤-total′</a> <a id="13867" class="Symbol">(</a><a id="13868" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13872" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13872" class="Bound">m</a><a id="13873" class="Symbol">)</a> <a id="13875" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>     <a id="13884" class="Symbol">=</a>  <a id="13887" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="13895" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="13899" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13782" class="Function">≤-total′</a> <a id="13908" class="Symbol">(</a><a id="13909" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13913" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13913" class="Bound">m</a><a id="13914" class="Symbol">)</a> <a id="13916" class="Symbol">(</a><a id="13917" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13921" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13921" class="Bound">n</a><a id="13922" class="Symbol">)</a>  <a id="13925" class="Symbol">=</a>  <a id="13928" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13960" class="Function">helper</a> <a id="13935" class="Symbol">(</a><a id="13936" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13782" class="Function">≤-total′</a> <a id="13945" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13913" class="Bound">m</a> <a id="13947" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13921" class="Bound">n</a><a id="13948" class="Symbol">)</a>
  <a id="13952" class="Keyword">where</a>
  <a id="13960" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13960" class="Function">helper</a> <a id="13967" class="Symbol">:</a> <a id="13969" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="13975" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13913" class="Bound">m</a> <a id="13977" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13921" class="Bound">n</a> <a id="13979" class="Symbol">→</a> <a id="13981" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="13987" class="Symbol">(</a><a id="13988" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="13992" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13913" class="Bound">m</a><a id="13993" class="Symbol">)</a> <a id="13995" class="Symbol">(</a><a id="13996" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14000" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13921" class="Bound">n</a><a id="14001" class="Symbol">)</a>
  <a id="14005" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13960" class="Function">helper</a> <a id="14012" class="Symbol">(</a><a id="14013" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="14021" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14021" class="Bound">m≤n</a><a id="14024" class="Symbol">)</a>  <a id="14027" class="Symbol">=</a>  <a id="14030" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="14038" class="Symbol">(</a><a id="14039" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="14043" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14021" class="Bound">m≤n</a><a id="14046" class="Symbol">)</a>
  <a id="14050" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#13960" class="Function">helper</a> <a id="14057" class="Symbol">(</a><a id="14058" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="14066" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14066" class="Bound">n≤m</a><a id="14069" class="Symbol">)</a>  <a id="14072" class="Symbol">=</a>  <a id="14075" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="14083" class="Symbol">(</a><a id="14084" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="14088" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14066" class="Bound">n≤m</a><a id="14091" class="Symbol">)</a>{% endraw %}</pre>
This is also our first use of a `where` clause in Agda.  The keyword `where` is
followed by one or more definitions, which must be indented.  Any variables
bound of the left-hand side of the preceding equation (in this case, `m` and
`n`) are in scope within the nested definition, and any identifiers bound in the
nested definition (in this case, `helper`) are in scope in the right-hand side
of the preceding equation.

If both arguments are equal, then both cases hold and we could return evidence
of either.  In the code above we return the forward case, but there is a
variant that returns the flipped case.
<pre class="Agda">{% raw %}<a id="≤-total″"></a><a id="14729" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14729" class="Function">≤-total″</a> <a id="14738" class="Symbol">:</a> <a id="14740" class="Symbol">∀</a> <a id="14742" class="Symbol">(</a><a id="14743" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14743" class="Bound">m</a> <a id="14745" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14745" class="Bound">n</a> <a id="14747" class="Symbol">:</a> <a id="14749" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="14750" class="Symbol">)</a> <a id="14752" class="Symbol">→</a> <a id="14754" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10792" class="Datatype">Total</a> <a id="14760" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14743" class="Bound">m</a> <a id="14762" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14745" class="Bound">n</a>
<a id="14764" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14729" class="Function">≤-total″</a> <a id="14773" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14773" class="Bound">m</a>       <a id="14781" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>                      <a id="14807" class="Symbol">=</a>  <a id="14810" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="14818" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="14822" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14729" class="Function">≤-total″</a> <a id="14831" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="14839" class="Symbol">(</a><a id="14840" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14844" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14844" class="Bound">n</a><a id="14845" class="Symbol">)</a>                   <a id="14865" class="Symbol">=</a>  <a id="14868" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="14876" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1441" class="InductiveConstructor">z≤n</a>
<a id="14880" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14729" class="Function">≤-total″</a> <a id="14889" class="Symbol">(</a><a id="14890" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14894" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14894" class="Bound">m</a><a id="14895" class="Symbol">)</a> <a id="14897" class="Symbol">(</a><a id="14898" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="14902" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14902" class="Bound">n</a><a id="14903" class="Symbol">)</a> <a id="14905" class="Keyword">with</a> <a id="14910" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14729" class="Function">≤-total″</a> <a id="14919" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14894" class="Bound">m</a> <a id="14921" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14902" class="Bound">n</a>
<a id="14923" class="Symbol">...</a>                        <a id="14950" class="Symbol">|</a> <a id="14952" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="14960" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14960" class="Bound">m≤n</a>   <a id="14966" class="Symbol">=</a>  <a id="14969" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10823" class="InductiveConstructor">forward</a> <a id="14977" class="Symbol">(</a><a id="14978" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="14982" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#14960" class="Bound">m≤n</a><a id="14985" class="Symbol">)</a>
<a id="14987" class="Symbol">...</a>                        <a id="15014" class="Symbol">|</a> <a id="15016" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="15024" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15024" class="Bound">n≤m</a>   <a id="15030" class="Symbol">=</a>  <a id="15033" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#10880" class="InductiveConstructor">flipped</a> <a id="15041" class="Symbol">(</a><a id="15042" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="15046" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15024" class="Bound">n≤m</a><a id="15049" class="Symbol">)</a>{% endraw %}</pre>
It differs from the original code in that it pattern
matches on the second argument before the first argument.


## Monotonicity

If one bumps into both an operator and an ordering at a party, one may ask if
the operator is _monotonic_ with regard to the ordering.  For example, addition
is monotonic with regard to inequality, meaning

    ∀ {m n p q : ℕ} → m ≤ n → p ≤ q → m + p ≤ n + q

The proof is straightforward using the techniques we have learned, and is best
broken into three parts. First, we deal with the special case of showing
addition is monotonic on the right.
<pre class="Agda">{% raw %}<a id="+-monoʳ-≤"></a><a id="15653" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15653" class="Function">+-monoʳ-≤</a> <a id="15663" class="Symbol">:</a> <a id="15665" class="Symbol">∀</a> <a id="15667" class="Symbol">(</a><a id="15668" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15668" class="Bound">m</a> <a id="15670" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15670" class="Bound">p</a> <a id="15672" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15672" class="Bound">q</a> <a id="15674" class="Symbol">:</a> <a id="15676" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="15677" class="Symbol">)</a>
  <a id="15681" class="Symbol">→</a> <a id="15683" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15670" class="Bound">p</a> <a id="15685" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="15687" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15672" class="Bound">q</a>
    <a id="15693" class="Comment">-------------</a>
  <a id="15709" class="Symbol">→</a> <a id="15711" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15668" class="Bound">m</a> <a id="15713" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="15715" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15670" class="Bound">p</a> <a id="15717" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="15719" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15668" class="Bound">m</a> <a id="15721" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="15723" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15672" class="Bound">q</a>
<a id="15725" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15653" class="Function">+-monoʳ-≤</a> <a id="15735" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>    <a id="15743" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15743" class="Bound">p</a> <a id="15745" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15745" class="Bound">q</a> <a id="15747" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15747" class="Bound">p≤q</a>  <a id="15752" class="Symbol">=</a>  <a id="15755" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15747" class="Bound">p≤q</a>
<a id="15759" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15653" class="Function">+-monoʳ-≤</a> <a id="15769" class="Symbol">(</a><a id="15770" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="15774" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15774" class="Bound">m</a><a id="15775" class="Symbol">)</a> <a id="15777" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15777" class="Bound">p</a> <a id="15779" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15779" class="Bound">q</a> <a id="15781" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15781" class="Bound">p≤q</a>  <a id="15786" class="Symbol">=</a>  <a id="15789" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1490" class="InductiveConstructor">s≤s</a> <a id="15793" class="Symbol">(</a><a id="15794" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15653" class="Function">+-monoʳ-≤</a> <a id="15804" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15774" class="Bound">m</a> <a id="15806" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15777" class="Bound">p</a> <a id="15808" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15779" class="Bound">q</a> <a id="15810" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15781" class="Bound">p≤q</a><a id="15813" class="Symbol">)</a>{% endraw %}</pre>
The proof is by induction on the first argument.

* _Base case_: The first argument is `zero` in which case
  `zero + p ≤ zero + q` simplifies to `p ≤ q`, the evidence
  for which is given by the argument `p≤q`.

* _Inductive case_: The first argument is `suc m`, in which case
  `suc m + p ≤ suc m + q` simplifies to `suc (m + p) ≤ suc (m + q)`.
  The inductive hypothesis `+-monoʳ-≤ m p q p≤q` establishes that
  `m + p ≤ m + q`, and our goal follows by applying `s≤s`.

Second, we deal with the special case of showing addition is
monotonic on the left. This follows from the previous
result and the commutativity of addition.
<pre class="Agda">{% raw %}<a id="+-monoˡ-≤"></a><a id="16469" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16469" class="Function">+-monoˡ-≤</a> <a id="16479" class="Symbol">:</a> <a id="16481" class="Symbol">∀</a> <a id="16483" class="Symbol">(</a><a id="16484" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16484" class="Bound">m</a> <a id="16486" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16486" class="Bound">n</a> <a id="16488" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16488" class="Bound">p</a> <a id="16490" class="Symbol">:</a> <a id="16492" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="16493" class="Symbol">)</a>
  <a id="16497" class="Symbol">→</a> <a id="16499" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16484" class="Bound">m</a> <a id="16501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16503" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16486" class="Bound">n</a>
    <a id="16509" class="Comment">-------------</a>
  <a id="16525" class="Symbol">→</a> <a id="16527" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16484" class="Bound">m</a> <a id="16529" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16531" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16488" class="Bound">p</a> <a id="16533" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16535" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16486" class="Bound">n</a> <a id="16537" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16539" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16488" class="Bound">p</a>
<a id="16541" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16469" class="Function">+-monoˡ-≤</a> <a id="16551" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16551" class="Bound">m</a> <a id="16553" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16553" class="Bound">n</a> <a id="16555" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16555" class="Bound">p</a> <a id="16557" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16557" class="Bound">m≤n</a>  <a id="16562" class="Keyword">rewrite</a> <a id="16570" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a> <a id="16577" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16551" class="Bound">m</a> <a id="16579" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16555" class="Bound">p</a> <a id="16581" class="Symbol">|</a> <a id="16583" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#8011" class="Function">+-comm</a> <a id="16590" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16553" class="Bound">n</a> <a id="16592" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16555" class="Bound">p</a>  <a id="16595" class="Symbol">=</a> <a id="16597" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15653" class="Function">+-monoʳ-≤</a> <a id="16607" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16555" class="Bound">p</a> <a id="16609" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16551" class="Bound">m</a> <a id="16611" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16553" class="Bound">n</a> <a id="16613" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16557" class="Bound">m≤n</a>{% endraw %}</pre>
Rewriting by `+-comm m p` and `+-comm n p` converts `m + p ≤ n + p` into
`p + m ≤ p + n`, which is proved by invoking `+-monoʳ-≤ p m n m≤n`.

Third, we combine the two previous results.
<pre class="Agda">{% raw %}<a id="+-mono-≤"></a><a id="16827" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16827" class="Function">+-mono-≤</a> <a id="16836" class="Symbol">:</a> <a id="16838" class="Symbol">∀</a> <a id="16840" class="Symbol">(</a><a id="16841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16841" class="Bound">m</a> <a id="16843" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16843" class="Bound">n</a> <a id="16845" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16845" class="Bound">p</a> <a id="16847" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16847" class="Bound">q</a> <a id="16849" class="Symbol">:</a> <a id="16851" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="16852" class="Symbol">)</a>
  <a id="16856" class="Symbol">→</a> <a id="16858" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16841" class="Bound">m</a> <a id="16860" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16862" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16843" class="Bound">n</a>
  <a id="16866" class="Symbol">→</a> <a id="16868" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16845" class="Bound">p</a> <a id="16870" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16872" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16847" class="Bound">q</a>
    <a id="16878" class="Comment">-------------</a>
  <a id="16894" class="Symbol">→</a> <a id="16896" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16841" class="Bound">m</a> <a id="16898" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16900" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16845" class="Bound">p</a> <a id="16902" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#1414" class="Datatype Operator">≤</a> <a id="16904" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16843" class="Bound">n</a> <a id="16906" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="16908" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16847" class="Bound">q</a>
<a id="16910" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16827" class="Function">+-mono-≤</a> <a id="16919" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16919" class="Bound">m</a> <a id="16921" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16921" class="Bound">n</a> <a id="16923" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16923" class="Bound">p</a> <a id="16925" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16925" class="Bound">q</a> <a id="16927" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16927" class="Bound">m≤n</a> <a id="16931" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16931" class="Bound">p≤q</a>  <a id="16936" class="Symbol">=</a>  <a id="16939" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#7488" class="Function">≤-trans</a> <a id="16947" class="Symbol">(</a><a id="16948" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16469" class="Function">+-monoˡ-≤</a> <a id="16958" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16919" class="Bound">m</a> <a id="16960" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16921" class="Bound">n</a> <a id="16962" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16923" class="Bound">p</a> <a id="16964" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16927" class="Bound">m≤n</a><a id="16967" class="Symbol">)</a> <a id="16969" class="Symbol">(</a><a id="16970" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#15653" class="Function">+-monoʳ-≤</a> <a id="16980" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16921" class="Bound">n</a> <a id="16982" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16923" class="Bound">p</a> <a id="16984" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16925" class="Bound">q</a> <a id="16986" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#16931" class="Bound">p≤q</a><a id="16989" class="Symbol">)</a>{% endraw %}</pre>
Invoking `+-monoˡ-≤ m n p m≤n` proves `m + p ≤ n + p` and invoking
`+-monoʳ-≤ n p q p≤q` proves `n + p ≤ n + q`, and combining these with
transitivity proves `m + p ≤ n + q`, as was to be shown.


#### Exercise `*-mono-≤` (stretch)

Show that multiplication is monotonic with regard to inequality.


## Strict inequality {#strict-inequality}

We can define strict inequality similarly to inequality.
<pre class="Agda">{% raw %}<a id="17415" class="Keyword">infix</a> <a id="17421" class="Number">4</a> <a id="17423" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17433" class="Datatype Operator">_&lt;_</a>

<a id="17428" class="Keyword">data</a> <a id="_&lt;_"></a><a id="17433" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17433" class="Datatype Operator">_&lt;_</a> <a id="17437" class="Symbol">:</a> <a id="17439" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="17441" class="Symbol">→</a> <a id="17443" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="17445" class="Symbol">→</a> <a id="17447" class="PrimitiveType">Set</a> <a id="17451" class="Keyword">where</a>

  <a id="_&lt;_.z&lt;s"></a><a id="17460" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17460" class="InductiveConstructor">z&lt;s</a> <a id="17464" class="Symbol">:</a> <a id="17466" class="Symbol">∀</a> <a id="17468" class="Symbol">{</a><a id="17469" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17469" class="Bound">n</a> <a id="17471" class="Symbol">:</a> <a id="17473" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="17474" class="Symbol">}</a>
      <a id="17482" class="Comment">------------</a>
    <a id="17499" class="Symbol">→</a> <a id="17501" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a> <a id="17506" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17433" class="Datatype Operator">&lt;</a> <a id="17508" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="17512" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17469" class="Bound">n</a>

  <a id="_&lt;_.s&lt;s"></a><a id="17517" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17517" class="InductiveConstructor">s&lt;s</a> <a id="17521" class="Symbol">:</a> <a id="17523" class="Symbol">∀</a> <a id="17525" class="Symbol">{</a><a id="17526" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17526" class="Bound">m</a> <a id="17528" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17528" class="Bound">n</a> <a id="17530" class="Symbol">:</a> <a id="17532" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="17533" class="Symbol">}</a>
    <a id="17539" class="Symbol">→</a> <a id="17541" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17526" class="Bound">m</a> <a id="17543" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17433" class="Datatype Operator">&lt;</a> <a id="17545" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17528" class="Bound">n</a>
      <a id="17553" class="Comment">-------------</a>
    <a id="17571" class="Symbol">→</a> <a id="17573" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="17577" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17526" class="Bound">m</a> <a id="17579" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17433" class="Datatype Operator">&lt;</a> <a id="17581" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="17585" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#17528" class="Bound">n</a>{% endraw %}</pre>
The key difference is that zero is less than the successor of an
arbitrary number, but is not less than zero.

Clearly, strict inequality is not reflexive. However it is
_irreflexive_ in that `n < n` never holds for any value of `n`.
Like inequality, strict inequality is transitive.
Strict inequality is not total, but satisfies the closely related property of
_trichotomy_: for any `m` and `n`, exactly one of `m < n`, `m ≡ n`, or `m > n`
holds (where we define `m > n` to hold exactly when `n < m`).
It is also monotonic with regards to addition and multiplication.

Most of the above are considered in exercises below.  Irreflexivity
requires negation, as does the fact that the three cases in
trichotomy are mutually exclusive, so those points are deferred to
Chapter [Negation]({{ site.baseurl }}{% link out/plfa/Negation.md%}).

It is straightforward to show that `suc m ≤ n` implies `m < n`,
and conversely.  One can then give an alternative derivation of the
properties of strict inequality, such as transitivity, by directly
exploiting the corresponding properties of inequality.

#### Exercise `<-trans` (recommended) {#less-trans}

Show that strict inequality is transitive.

#### Exercise `trichotomy` {#trichotomy}

Show that strict inequality satisfies a weak version of trichotomy, in
the sense that for any `m` and `n` that one of the following holds:
  * `m < n`,
  * `m ≡ n`, or
  * `m > n`
Define `m > n` to be the same as `n < m`.
You will need a suitable data declaration,
similar to that used for totality.
(We will show that the three cases are exclusive after we introduce
[negation]({{ site.baseurl }}{% link out/plfa/Negation.md%}).)

#### Exercise `+-mono-<` {#plus-mono-less}

Show that addition is monotonic with respect to strict inequality.
As with inequality, some additional definitions may be required.

#### Exercise `≤-iff-<` (recommended) {#leq-iff-less}

Show that `suc m ≤ n` implies `m < n`, and conversely.

#### Exercise `<-trans-revisited` {#less-trans-revisited}

Give an alternative proof that strict inequality is transitive,
using the relating between strict inequality and inequality and
the fact that inequality is transitive.


## Even and odd

As a further example, let's specify even and odd numbers.  Inequality
and strict inequality are _binary relations_, while even and odd are
_unary relations_, sometimes called _predicates_.
<pre class="Agda">{% raw %}<a id="19926" class="Keyword">data</a> <a id="even"></a><a id="19931" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="19936" class="Symbol">:</a> <a id="19938" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="19940" class="Symbol">→</a> <a id="19942" class="PrimitiveType">Set</a>
<a id="19946" class="Keyword">data</a> <a id="odd"></a><a id="19951" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19951" class="Datatype">odd</a>  <a id="19956" class="Symbol">:</a> <a id="19958" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a> <a id="19960" class="Symbol">→</a> <a id="19962" class="PrimitiveType">Set</a>

<a id="19967" class="Keyword">data</a> <a id="19972" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="19977" class="Keyword">where</a>

  <a id="even.zero"></a><a id="19986" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19986" class="InductiveConstructor">zero</a> <a id="19991" class="Symbol">:</a>
      <a id="19999" class="Comment">---------</a>
      <a id="20015" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="20020" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#115" class="InductiveConstructor">zero</a>

  <a id="even.suc"></a><a id="20028" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20028" class="InductiveConstructor">suc</a>  <a id="20033" class="Symbol">:</a> <a id="20035" class="Symbol">∀</a> <a id="20037" class="Symbol">{</a><a id="20038" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20038" class="Bound">n</a> <a id="20040" class="Symbol">:</a> <a id="20042" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="20043" class="Symbol">}</a>
    <a id="20049" class="Symbol">→</a> <a id="20051" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19951" class="Datatype">odd</a> <a id="20055" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20038" class="Bound">n</a>
      <a id="20063" class="Comment">------------</a>
    <a id="20080" class="Symbol">→</a> <a id="20082" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="20087" class="Symbol">(</a><a id="20088" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="20092" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20038" class="Bound">n</a><a id="20093" class="Symbol">)</a>

<a id="20096" class="Keyword">data</a> <a id="20101" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19951" class="Datatype">odd</a> <a id="20105" class="Keyword">where</a>

  <a id="odd.suc"></a><a id="20114" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="InductiveConstructor">suc</a>   <a id="20120" class="Symbol">:</a> <a id="20122" class="Symbol">∀</a> <a id="20124" class="Symbol">{</a><a id="20125" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20125" class="Bound">n</a> <a id="20127" class="Symbol">:</a> <a id="20129" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="20130" class="Symbol">}</a>
    <a id="20136" class="Symbol">→</a> <a id="20138" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="20143" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20125" class="Bound">n</a>
      <a id="20151" class="Comment">-----------</a>
    <a id="20167" class="Symbol">→</a> <a id="20169" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19951" class="Datatype">odd</a> <a id="20173" class="Symbol">(</a><a id="20174" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#128" class="InductiveConstructor">suc</a> <a id="20178" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20125" class="Bound">n</a><a id="20179" class="Symbol">)</a>{% endraw %}</pre>
A number is even if it is zero or the successor of an odd number,
and odd if it is the successor of an even number.

This is our first use of a mutually recursive datatype declaration.
Since each identifier must be defined before it is used, we first
declare the indexed types `even` and `odd` (omitting the `where`
keyword and the declarations of the constructors) and then
declare the constructors (omitting the signatures `ℕ → Set`
which were given earlier).

This is also our first use of _overloaded_ constructors,
that is, using the same name for constructors of different types.
Here `suc` means one of three constructors:

    suc : `ℕ → `ℕ

    suc : ∀ {n : ℕ}
      → odd n
        ------------
      → even (suc n)

    suc : ∀ {n : ℕ}
      → even n
        -----------
      → odd (suc n)

Similarly, `zero` refers to one of two constructors. Due to how it
does type inference, Agda does not allow overloading of defined names,
but does allow overloading of constructors.  It is recommended that
one restrict overloading to related meanings, as we have done here,
but it is not required.

We show that the sum of two even numbers is even.
<pre class="Agda">{% raw %}<a id="e+e≡e"></a><a id="21357" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21357" class="Function">e+e≡e</a> <a id="21363" class="Symbol">:</a> <a id="21365" class="Symbol">∀</a> <a id="21367" class="Symbol">{</a><a id="21368" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21368" class="Bound">m</a> <a id="21370" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21370" class="Bound">n</a> <a id="21372" class="Symbol">:</a> <a id="21374" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21375" class="Symbol">}</a>
  <a id="21379" class="Symbol">→</a> <a id="21381" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="21386" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21368" class="Bound">m</a>
  <a id="21390" class="Symbol">→</a> <a id="21392" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="21397" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21370" class="Bound">n</a>
    <a id="21403" class="Comment">------------</a>
  <a id="21418" class="Symbol">→</a> <a id="21420" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="21425" class="Symbol">(</a><a id="21426" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21368" class="Bound">m</a> <a id="21428" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21430" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21370" class="Bound">n</a><a id="21431" class="Symbol">)</a>

<a id="o+e≡o"></a><a id="21434" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21434" class="Function">o+e≡o</a> <a id="21440" class="Symbol">:</a> <a id="21442" class="Symbol">∀</a> <a id="21444" class="Symbol">{</a><a id="21445" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21445" class="Bound">m</a> <a id="21447" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21447" class="Bound">n</a> <a id="21449" class="Symbol">:</a> <a id="21451" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#97" class="Datatype">ℕ</a><a id="21452" class="Symbol">}</a>
  <a id="21456" class="Symbol">→</a> <a id="21458" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19951" class="Datatype">odd</a> <a id="21462" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21445" class="Bound">m</a>
  <a id="21466" class="Symbol">→</a> <a id="21468" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19931" class="Datatype">even</a> <a id="21473" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21447" class="Bound">n</a>
    <a id="21479" class="Comment">-----------</a>
  <a id="21493" class="Symbol">→</a> <a id="21495" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19951" class="Datatype">odd</a> <a id="21499" class="Symbol">(</a><a id="21500" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21445" class="Bound">m</a> <a id="21502" href="https://agda.github.io/agda-stdlib/Agda.Builtin.Nat.html#230" class="Primitive Operator">+</a> <a id="21504" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21447" class="Bound">n</a><a id="21505" class="Symbol">)</a>

<a id="21508" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21357" class="Function">e+e≡e</a> <a id="21514" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#19986" class="InductiveConstructor">zero</a>     <a id="21523" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21523" class="Bound">en</a>  <a id="21527" class="Symbol">=</a>  <a id="21530" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21523" class="Bound">en</a>
<a id="21533" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21357" class="Function">e+e≡e</a> <a id="21539" class="Symbol">(</a><a id="21540" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20028" class="InductiveConstructor">suc</a> <a id="21544" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21544" class="Bound">om</a><a id="21546" class="Symbol">)</a> <a id="21548" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21548" class="Bound">en</a>  <a id="21552" class="Symbol">=</a>  <a id="21555" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20028" class="InductiveConstructor">suc</a> <a id="21559" class="Symbol">(</a><a id="21560" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21434" class="Function">o+e≡o</a> <a id="21566" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21544" class="Bound">om</a> <a id="21569" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21548" class="Bound">en</a><a id="21571" class="Symbol">)</a>

<a id="21574" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21434" class="Function">o+e≡o</a> <a id="21580" class="Symbol">(</a><a id="21581" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="InductiveConstructor">suc</a> <a id="21585" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21585" class="Bound">em</a><a id="21587" class="Symbol">)</a> <a id="21589" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21589" class="Bound">en</a>  <a id="21593" class="Symbol">=</a>  <a id="21596" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#20114" class="InductiveConstructor">suc</a> <a id="21600" class="Symbol">(</a><a id="21601" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21357" class="Function">e+e≡e</a> <a id="21607" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21585" class="Bound">em</a> <a id="21610" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/Relations.md %}{% raw %}#21589" class="Bound">en</a><a id="21612" class="Symbol">)</a>{% endraw %}</pre>
Corresponding to the mutually recursive types, we use two mutually recursive
functions, one to show that the sum of two even numbers is even, and the other
to show that the sum of an odd and an even number is odd.

This is our first use of mutually recursive functions.  Since each identifier
must be defined before it is used, we first give the signatures for both
functions and then the equations that define them.

To show that the sum of two even numbers is even, consider the
evidence that the first number is even. If it is because it is zero,
then the sum is even because the second number is even.  If it is
because it is the successor of an odd number, then the result is even
because it is the successor of the sum of an odd and an even number,
which is odd.

To show that the sum of an odd and even number is odd, consider the
evidence that the first number is odd. If it is because it is the
successor of an even number, then the result is odd because it is the
successor of the sum of two even numbers, which is even.

#### Exercise `o+o≡e` (stretch) {#odd-plus-odd}

Show that the sum of two odd numbers is even.

#### Exercise `Bin-predicates` (stretch) {#Bin-predicates}

Recall that 
Exercise [Bin]({{ site.baseurl }}{% link out/plfa/Naturals.md%}#Bin)
defines a datatype `Bin` of bitstrings representing natural numbers.
Representations are not unique due to leading zeros.
Hence, eleven may be represented by both of the following

    x1 x1 x0 x1 nil
    x1 x1 x0 x1 x0 x0 nil

Define a predicate

    Can : Bin → Set

over all bitstrings that holds if the bitstring is canonical, meaning
it has no leading zeros; the first representation of eleven above is
canonical, and the second is not.  To define it, you will need an
auxiliary predicate

    One : Bin → Set

that holds only if the bistring has a leading one.  A bitstring is
canonical if it has a leading one (representing a positive number) or
if it consists of a single zero (representing zero).

Show that increment preserves canonical bitstrings.

    Can x
    ------------
    Can (inc x)

Show that converting a natural to a bitstring always yields a
canonical bitstring.

    ----------
    Can (to n)

Show that converting a canonical bitstring to a natural
and back is the identity.

    Can x
    ---------------
    to (from x) ≡ x

(Hint: For each of these, you may first need to prove related
properties of `One`.)

## Standard prelude

Definitions similar to those in this chapter can be found in the standard library.
<pre class="Agda">{% raw %}<a id="24115" class="Keyword">import</a> <a id="24122" href="https://agda.github.io/agda-stdlib/Data.Nat.html" class="Module">Data.Nat</a> <a id="24131" class="Keyword">using</a> <a id="24137" class="Symbol">(</a><a id="24138" href="https://agda.github.io/agda-stdlib/Data.Nat.Base.html#742" class="Datatype Operator">_≤_</a><a id="24141" class="Symbol">;</a> <a id="24143" href="https://agda.github.io/agda-stdlib/Data.Nat.Base.html#765" class="InductiveConstructor">z≤n</a><a id="24146" class="Symbol">;</a> <a id="24148" href="https://agda.github.io/agda-stdlib/Data.Nat.Base.html#807" class="InductiveConstructor">s≤s</a><a id="24151" class="Symbol">)</a>
<a id="24153" class="Keyword">import</a> <a id="24160" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="24180" class="Keyword">using</a> <a id="24186" class="Symbol">(</a><a id="24187" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#1804" class="Function">≤-refl</a><a id="24193" class="Symbol">;</a> <a id="24195" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#1997" class="Function">≤-trans</a><a id="24202" class="Symbol">;</a> <a id="24204" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#1854" class="Function">≤-antisym</a><a id="24213" class="Symbol">;</a> <a id="24215" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#2109" class="Function">≤-total</a><a id="24222" class="Symbol">;</a>
                                  <a id="24258" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#10952" class="Function">+-monoʳ-≤</a><a id="24267" class="Symbol">;</a> <a id="24269" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#10862" class="Function">+-monoˡ-≤</a><a id="24278" class="Symbol">;</a> <a id="24280" href="https://agda.github.io/agda-stdlib/Data.Nat.Properties.html#10706" class="Function">+-mono-≤</a><a id="24288" class="Symbol">)</a>{% endraw %}</pre>
In the standard library, `≤-total` is formalised in terms of
disjunction (which we define in
Chapter [Connectives]({{ site.baseurl }}{% link out/plfa/Connectives.md%})),
and `+-monoʳ-≤`, `+-monoˡ-≤`, `+-mono-≤` are proved differently than here,
and more arguments are implicit.


## Unicode

This chapter uses the following unicode.

    ≤  U+2264  LESS-THAN OR EQUAL TO (\<=, \le)
    ≥  U+2265  GREATER-THAN OR EQUAL TO (\>=, \ge)
    ˡ  U+02E1  MODIFIER LETTER SMALL L (\^l)
    ʳ  U+02B3  MODIFIER LETTER SMALL R (\^r)

The commands `\^l` and `\^r` give access to a variety of superscript
leftward and rightward arrows in addition to superscript letters `l` and `r`.
