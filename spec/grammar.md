# Exclaim's Grammar

## Spec

The following is the current working grammar specification for exclaim. The spec is an untested rough draft and is subject to breaking changes.

### Blocks

Blocks are the starting and endpoint for every piece of exclaim's functionality. Unlike other template languages, exclaim only has one type of syntax to denote the existence of a block: ```{{ }}```.  

Two left curly-brackets to start the block ```{{```, and every open block gets matched with a closing block ```}}```. Other languages have multiple syntax structures to differentiate between data binding and control flow. E.g. using ```{{ }}``` and ```{% %}```. As a user, I have never been fond of multiple syntax structures. The design makes more sense for the designer rather than the user. A single syntax lends to a streamlined workflow.

The grammar for a Block:

```none
TEXT -> string_literal TEXT 
      | {{ BLOCK_STMT }} TEXT
      | ɛ
```

### Inside Blocks

Every single block declaration will contain a single command. A command comprises two parts: an action and an expression. In the future, the compiler will infer the action by deriving the semantic meaning of the expression (see *action elision* below).  

**View the appendix below to understand the format of the grammar definitions and terminal symbol definition.**

```none
BLOCK_STMT -> END_STMT
            | LET_STMT
            | RENDER_STMT
            | WRITE_STMT

END_STMT -> !

LET_STMT -> let! LET_EXPR
LET_EXPR -> PATTERN = EXPR

RENDER_STMT -> render! RENDER_EXPR
RENDER_EXPR -> ITER_RENDER_EXPR
             | ɛ

ITER_RENDER_EXPR -> PATTERN : EXPR

WRITE_STMT -> write! EXPR.

EXPR -> REF EXPR'
      | LITERAL_EXPR EXPR'

EXPR' -> PIPE
       | ɛ

PIPE -> | FN_CALL PIPE'
PIPE' -> | FN_CALL
       | ɛ

FN_CALL -> LABEL ARGS

ARGS -> ( ARG_LIST )
      | ɛ

ARG_LIST -> EXPR ARG_LIST'
ARG_LIST' -> , EXPR

PATTERN -> LABEL
         | ( PAT_LIST )
PAT_LIST -> LABEL PAT_LIST'
PAT_LIST' -> , LABEL
           | ɛ

REF -> LABEL REF_PRIME
REF_PRIME -> .LABEL
           | ɛ

LITERAL_EXPR -> INTEGER_LITERAL
LITERAL_EXPR -> "STRING_LITERAL"

INTEGER_LITERAL -> integer
STRING_LITERAL -> str
LABEL -> id
```

## Incomplete Specs

### Fallback

If you want to perform an iter-render, but the iterator is empty, there should be a fallback clause to render something instead of nothing.  
Example:

```none
{{ render! item : site.shop.items }}
    <li>{{ items }}</li>
{{ fallback! }}
    <li>The shop is empty.</li>
{{!}}
```

### Action Elision

Action elision borrows from the idea of lifetime elision in Rust. In unambiguous cases, the user may omit the action while keeping functionality intact - increases workflow.

Example:  
```{{ assign! posts = site.posts }}```  
assign! is the only action that would contain the assignment operator '```=```'.  

With this in mind, we can omit ```assign!``` and keep the functionality without ambiguity.  
```{{ posts = site.posts }}```

### Closures

Closures: we could filter using a closure. Without a closure, we have no way to create a useful filter. Closures are a work in progress because I haven't nailed down the structure nor all use cases.  

The current working idea for closures:  
```{{ site.posts | filter [self.data.year() == 2020] }} // Get all posts made in 2020```

The syntax is simple, but there are some undecided behaviors of closures:

1. What kind of expressions will be allowed? Just conditional? Multiline structures?
2. How do closures interact with their environment? Do we allow captures? If so, how would that work? (Lazily capture?)

## Appendix

### Grammar Format

**ALL_CAPS**: Non-terminal symbols  
**lower_case**: Terminal symbols

### Grammar Terminal Symbol Definitions

**str**: A static string of Unicode characters.  
**id**: A name assigned to an element in the program.  
**integer**: just a plain integer
