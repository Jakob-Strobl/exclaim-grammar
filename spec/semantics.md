# Exclaim's Semantics

## Spec

The following are the current working semantic rules for Exclaim. The spec is an untested rough draft and is subject to breaking changes.

### Blocks

Exclaim blocks come in two flavors:

- Text Block
  - A block containing text from the template. No code.
- Code Block
  - A block containing Exclaim code.

Text blocks are straightforward, and during semantic analysis, every text block is sound.

On the other hand, code blocks have various rules to satisfy before deemed sound.

### Actions / Statements

Every code block contains a single statement, each statement contains one action, and each action conveys the purpose of that block.

Currently, there are four types of actions/statements:

- End
  - ```{{!}}```
- Let
  - ```{{ let! a = b }}```
    - Where: ```a``` is a pattern and ```b``` is an expression.
- Render
  - ```{{ render! a : b }}```
    - Where: ```a``` is a pattern and ```b``` is an iterable expression.
- Write
  - ```{{ write! a }}```
    - Where: ```a``` is a serializable expression

While the implementation might not explicitly define a statement for each action, in the end, **each action maps to its own statement.**

#### Write Block

Write blocks are probably the most straightforward block in the language. The resulting value of the expression in a write block is rendered output. Write blocks are [self-containing](#self-containing-blocks). 

The expression in the Write block must be at the very least serializable. If not, it will throw an error during semantic analysis (worst case during rendering).

There's only one case where a Write block would fail semantic analysis off the top of my head. If we try to print a reference, and Exclaim can't resolve the reference to an actual piece of data during data-binding, then the block is not sound.

#### Let Block

Let blocks allow you to bind an expression to a pattern. Similar to many dynamic languages, the user does not have to worry about types. Let blocks are [self-containing](#self-containing-blocks).

You can declare [patterns](#patterns) of the same names in or out of the same scope. Let's look at the following valid code blocks:

```None
{{ let! test = 123 }}
{{ write! test }}
{{ let! test = "Hello!" }}
{{ write! test}}
```
The output is: 
```
123
Hello!
```

There is no issue with declaring the same name twice. Scoped variables work similarly to many programming languages, where the variable declaration only exists inside of that scope and nested scopes ([variable shadowing is supported](https://en.wikipedia.org/wiki/Variable_shadowing)).

#### Render Block

Render blocks loop on an iterator. 
An iterable expression binds to the pattern on the left-hand side. The expression must resolve to an iterator. Render blocks are not self-containing; they open a new scope. For each iteration, the body of the render block is rendered.

Here's an example using a render block:

```None
{{ render! (char, index) : "abcdefg" | split | enumerate }} // 'split' transformation turns a string into an iterator of unicode characters
<li>{{write! index}}: {{write! char}}</li>
{{!}} // Since render blocks open a new scope, we need to close it with an End block
```

The example renders the following output:

```None
<li>0: a</li>
<li>1: b</li>
<li>2: c</li>
<li>3: d</li>
<li>4: e</li>
<li>5: f</li>
<li>6: g</li>
```

#### End Block

End blocks are an oddball. All they do is close the current open scope. End blocks are not allowed in the file scope. Their purpose is to complete the scope created by a non-contained block. They should not contain an expression.

Example: End block closes scope opened by Render block
```
// File Scope
{{ render! a : b }} // Opens a new scope: "Render Scope"

// Body of the "Render Scope"

{{!}} // Closes "Render Scope"
// File Scope 
```

Example: End block in File scope
```
// File Scope
{{ let! a = b }}
{{!}} // ERROR: End block in the File scope 
// File Scope
```

### Self-Containing Blocks

Self-contained blocks do not open nor close a new scope. Let and Write are examples of self-containing blocks.

### Patterns

Patterns allow you to declare/bind one or more variables by either assigning or deconstructing the expression on the right-hand side.

Let's look at two examples to show the difference between assigning and deconstructing an expression:

```None
{{ render! page : pages | enumerate }} // Pages is an iterator, and the transformation 'enumerate' creates a new iterator that returns each element with its respective index

// So 'page' is now an iterator of tuples (pages-element, integer)
( {{write! page.0 }}, {{write! page.1 }} ) // serializes the tuple for the current element in the iterator 

{{!}}
```

Since ```enumerate``` tranforms the ```pages``` iterator into an iterator of tuples, we can deconstruct that tuple into the pattern.
```None
{{ render! (page, index) : pages | enumerate }} // Deconstructed current element into to variables 'page' and 'index'

// We deconstructed the tuple from the right-hand expression! 
({{ write! page }}, {{ write! index}})

{{!}}
```

For now, **pattern deconstructing works on only simple tuples.**
Exclaim's Patterns take a lot of inspiration from Rust's Patterns.

### Type Checking

> "If it looks like a duck and quacks like a duck, it's a duck."

Exclaim does not burden the user with handling types. The lack of need of defining types is possible because Exclaim's data-binding process simplifies any data into only a few types:

- Scalars
  - A primitive containing a single value
- Objects
  - A container holding one or more **named fields** of any type (scalars, objects, tuples, iterators).
- Tuples
  - A container holding one or more values of any type.
- Iterators
  - Anything that is iterable. 

The types only matter depending on the context of the code you are trying to render. Render statements need their expressions to resolve into iterators, and a nested reference, ```user.name```, needs ```user``` to be an object containing the field ```name```.

### Symbol Resolution

Symbols (aka Labels) are resolved during semantic analysis. Symbols are resolved from being defined inside the template file or in the data-context during data-binding. Type checking and symbol resolution occur in tandem. 