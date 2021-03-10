# Exclaim's Grammar

## Spec

The following is the current working grammar specification for exclaim. The spec is an untested rough draft and is subject to breaking changes.

### Blocks

Blocks are the starting and endpoint for every piece of exclaim's functionality. Unlike other template languages, exclaim only has one type of syntax to denote the existence of a block: ```{{ }}```.  

Two left curly-brackets to start the block ```{{```, and every open block gets matched with a closing block ```}}```. Other languages have multiple syntax structures to differentiate between data binding and control flow. E.g. using ```{{ }}``` and ```{% %}```. As a user, I have never been fond of multiple syntax structures. The design makes more sense for the designer rather than the user. A single syntax lends to a streamlined workflow.

The grammar for a Block:

```none
TEXT -> string_literal TEXT 
      | {{ BLOCK_COMMAND }} TEXT
```

### Inside Blocks

Every single block declaration will contain a single command. A command comprises two parts: an action tag and an expression. In the future, the compiler will infer the action by deriving the semantic meaning of the expression (see *action elision* below).  

**View the appendix below to understand the format of the grammar definitions and terminal symbol definition.**

```none
BLOCK_COMMAND -> ACTION EXPR 
               | EXPR

ACTION -> ACTION_LABEL!  
        | !  

ACTION_LABEL -> render 
              | assign
              | fallback
              | write

EXPR -> ASSIGNMENT
      | EACH
      | PIPE
      | PATTERN
      | VAR

ASSIGNMENT   -> = EXPR
EACH         -> : EXPR
PIPE         -> | FUNCTION
PATTERN      -> ( PATTERN_LIST )
PATTERN_LIST -> VAR, PATTERN_LIST
              | VAR
VAR          -> label.VAR
              | label

FUNCTION -> label ARG PIPE
          | label ARG

ARG -> ARGS_LIST

ARGS_LIST -> ARGUMENT, ARGS_LIST
           | ARGUMENT

ARGUMENT -> STRING_LITERAL
          | NUMBER_LITERAL
          | VAR

STRING_LITERAL -> " string_literal "
NUMBER_LITERAL -> number
```

## Incomplete Specs

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

**string_literal**: A static string of Unicode characters.
**label**: A word that will be related to a piece of data.  
**number**: integer
