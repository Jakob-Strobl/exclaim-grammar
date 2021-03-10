# Exclaim's Lexical Analysis

## Lexer

The lexer tokenizes Unicode input into a contiguous list of tokens passed to the parser.
In theory, you want the lexer to be context-free, but a template language inherently requires some form of context. The source code of a template language boils down to two parts: template code and plain text. In conventional languages, the source code is expected to fit the defined grammar of the language. Template languages need to differentiate between plain text and actual code.  

Without context, we wouldn't be able to differentiate between non-code and code. This limitation would force us to tokenize non-code as code. A context-free lexer offloads work from the lexer to the parser. The parser would read through and stitch together tokens of non-code until reaching parseable template code. It seemed unreasonable to sacrifice output quality, memory, and computation (We have to create and handle a lot more tokens) for the sake of a context-free lexer - which is why exclaim uses a context-sensitive lexer.  

Adding context increases the lexer's complexity, but the tokenized output is clean. Only one token, ```StringLiteral``` is used for plain text that doesn't relate to exclaim's language.

## Tokens

The lexer looks for the following kinds of tokens:

```none
StringLiteral
NumberLiteral
Label
Operator
```

### StringLiteral

String literals are either one of the two:

- A sequence of one or more Unicode characters surrounding exclaim statements (outside of exclaim statements)
- A sequence of zero or more Unicode characters enclosed within double quotation marks. 

### NumberLiteral

A sequence of one or more digits inside of a block.   
**Only integers are supported.** May change in the future.

123 is not a number literal in "```Testing 123```"  
123 is a Number literal in "```Testing {{ 123 }}```"

### Label

A sequence of characters that names a variable, function, or action. Also known as an identifier.

Labels can only consist of alphabetic Unicode characters and *underscore* '_'. Labels are case-sensitive.

### Operators

#### General Operators

```none
!   Action
=   Assign
}}  Block Close
{{  Block Open
,   Comma
.   Dot
:   Each
|   Pipe
```

#### Comparison Operators

```none
==  Equality 
!=  Inequality
>=  Greater than or equal to
>   Greater than
<=  Less than or equal to
<   Less than
```

#### Logical Operators

```none
&&  And
||  Or
```

#### Reserved Operators

Below are operators defined in *tokens.rs* that lack implementation in the compiler and language. 

```none
} Block Close Prime
{ Block Open Prime
[ Closure Open
] Closure Close 
```
