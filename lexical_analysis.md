# Lexical Analysis #


## Part 1 ##

We do lexical analysis to provide a syntax for our language. The input
is a series of bytes, and the output is a series of tokens.

We use tokens as the basic 'unit' of our syntax, similar to words &
punctuation in English. Tokens must be precisely specified using
patterns.


### Languages as Strings ###

* We define a string over an alphabet Σ (Capital Sigma) as a *finite*
  sequence of symbols from Σ

* ɛ is the empty string

* Concatenating ɛ with a string `s` will give `s`

* Σ* is the set of *all* strings over Σ

* A language L is a subset of Σ*


### Regular Expressions ###

A regex is either:
  1. ∅
  2. ɛ
  3. a, where a is some element of Σ
  4. R₁|R₂, where R₁ and R₂ are regular expressions
  5. R₁.R₂, where R₁ and R₂ are regular expressions
  6. (R), where R is a regular expression
  7. R*, where R is a regular expression

Regular expressions define a language (the set of all strings that the
regex matches).

The language L(R) of regular expression R is given by:
  1. L(∅) = ∅
  2. L(ɛ) = {ɛ}
  3. L(a) = {a}
  4. L(R₁|R₂) = L(R₁) ∪ L(R₁)
  5. L(R₁.R₂) = L(R₁) . L(R₁)
    A . B = {xy : x ∊ A and y ∊ B}
    e.g. A = {aa,b}, B = {a,b}
         A . B = {aaa, aab, ba, bb}
  6. L((R)) = L(R). Just like in math, we must decide that . has higher
     precedence than * in order to resolve ambiguities (e.g. a|b.c).
  7. L(R*) = 0 or more of a
     e.g. L(a*) = {Ɛ,a,aa,aaa, ...}

### Kleene Star ###

L(R*)

L⁰(R) = {ɛ}
Lⁱ(R) = Lⁱ⁻¹(R) . L(R)
L(R*) = ⋃ᵢ>=₀Lⁱ(R)

### Tokens ###

ID = letter . (letter | digit | _)*
DOT = \.
NUM = pdigit . (digit)* | 0
DECIMAL = NUM . DOT . digit . digit*

What is the first token in `"1.1abc1.1"`?

    "1.1abc1.1"  | Potential    | Match      | Notes
     ^           | DECIMAL, NUM | NUM(1)     |
     ^^          | DECIMAL      |     ∅      | Don't have to check ID
     ^^^         | DECIMAL      | DECIMAL(3) |
     ^^^^        |      ∅       |     ∅      |

The longest prefix match is `DECIMAL`: `"1.1"`.

And continuing:

    "abc1.1"  | Potential    | Match
     ^        | ID           | ID(1)
     ^^       | ID           | ID(2)
     ^^^      | ID           | ID(3)
     ^^^^     | ID           | ID(4)
     ^^^^^    | ∅            | ∅

    ".1"      | Potential    | Match
     ^        |              | DOT

    "1"       | Potential    | Match
     ^        |              | NUM

So `"1.1abc1.1"` -> `DECIMAL(1.1)`, `ID(abc1)`, `DOT`, `NUM(1)`.
