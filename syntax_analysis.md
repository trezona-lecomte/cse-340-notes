# Syntax Analysis #

Our goal is to transform the sequence of tokens produced by the lexer
into something useful. Therefore we need a way to tell if a sequence of
tokens is valid.


## Using Regular Expressions ##

Regular Expressions are not expressive enough to capture all programming
constructs, e.g. we cannot describe:

    L(R) = {ɛ,ab,aabb,aaabbb,...}

The set containing all strings that have equal numbers of a's and b's,
because regular expressions (as defined in CSE-340) have no concept of
counting.


## Context-Free Grammars ##

This can be thought of as relaxing the restriction on recursion that we
had in regex.

    S -> aSb
    S -> ɛ

a and b are terminal:

    S - starting non-terminal
    a - terminal
    A - non-terminal
    ɛ - terminal

Intuitively, if we don't have rules for something then it's terminal.

Syntax for context-free grammars:
  * Each row is called a production
    - Non-terminals on the left
    - Right arrow
    - Non-terminals and terminals on the right
  * Non-terminals start with upper case in these examples, terminals are
    lower case and are token

Example of derivation:

    S -> (S) | ɛ
    S => ɛ
    S => (S) => (ɛ) = ()

We start at the starting non-terminal, then apply rules until we can no
longer apply any more rules.

    Exp -> Exp + Exp
    Exp -> Exp * Exp
    Exp -> NUM

    Exp => Exp * Exp => Exp * 3 => Exp + Exp * 3 => Exp + 2 * 3 => 1 + 2 * 3

So there are different ways we can derive a string from a grammar.

    S -> aAb
    A -> abA | ɛ

    S => aAb => aabAb => aababAb => aabababAb => aabababɛb => aabababb

## Leftmost derivation ##

Always expand the leftmost non-terminal.

Is this a leftmost derivation?

    Exp => Exp * Exp => Exp * 3 => Exp + Exp * 3 => Exp + 2 * 3 => 1 + 2 * 3

No, because we expanded the rightmost non-terminal to 3.

    Exp => Exp * Exp => Exp + Exp * Exp => 1 + Exp * Exp => 1 + 2 * Exp => 1 + 2 * 3

This ^ is a leftmost derivation.


## Rightmost derivation ##

Always expand the rightmost non-terminal.


## Parse Trees ##

When we're doing derivations, we seem to be building up trees - we can
represent derivations using a parse tree.

We care about the order of children, because we end up concatenating the
leaves at the end of our derivation.

    S => E => E * E => E + E * E => 1 + E * E => 1 + 2 * E => 1 + 2 * 3

             S
             |
           __E__
          /  |  \
         E   *   E
       / | \     |
      E  +  E   NUM
      |     |
     NUM   NUM


Parsing is the process of determining the derivation or parse tree from
a sequence of tokens.

There are two major parsing problems:
 * Ambiguous grammars
 * Efficient parsing


## Ambiguous Grammars ##

A grammar is ambiguous if there exist two different parse trees (or two
different leftmost derivations or rightmost derivations) for the same
input.


## Parsing Approaches ##

Bottom-up parsing: start from the leaves (terminals) and work up.

Top-down parsing: start from the starting non-terminal and work down.

CSE-340 is about top-down parsing.


    S -> A | B | C
    A -> a
    B -> Bb | b
    C -> Cc | ɛ

```c
    parseS() {
        t_type = getToken();

        if (t_type == a) {
            ungetToken();
            parseA();
            checkEOF();
        }
    }

    checkEOF() {
        t_type = getToken();

        if (t_type != $) {
            syntaxError();
        }
    }
```
