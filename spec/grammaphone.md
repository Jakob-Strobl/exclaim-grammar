# Grammaphone

[Grammaphone](https://github.com/mdaines/grammophone) is a great tool made by **mdaines**.  

I forked the repo and added some features (escape characters) to brainstorm grammar ideas.  

The following grammar can be copy and pasted into my fork of [grammaphone](https://github.com/Jakob-Strobl/grammophone). The grammar will be a close approximation to the implemented version. Compared to the grammar defined in [grammar.md](grammar.md), the terminals are trimmed to reduce the number of permutations for better example sentences.  

```
BLOCK -> {{ BLOCK_STMT }}.

BLOCK_STMT -> END_STMT.
BLOCK_STMT -> LET_STMT.
BLOCK_STMT -> RENDER_STMT.
BLOCK_STMT -> WRITE_STMT.

# End Block
END_STMT -> !.

# let! block
LET_STMT -> let! LET_EXPR.
LET_EXPR -> PATTERN = EXPR.

# render! block
RENDER_STMT -> render! RENDER_EXPR.
RENDER_EXPR -> ITER_RENDER_EXPR.
RENDER_EXPR -> . # Null render expr

ITER_RENDER_EXPR -> PATTERN EACH.
EACH -> : EXPR.

# write! block
WRITE_STMT -> write! EXPR.

# Expressions
EXPR -> REF EXPR'.
EXPR -> LITERAL_EXPR EXPR'.

EXPR' -> PIPE.
EXPR' -> .

PIPE -> \| FN_CALL PIPE'.
PIPE' -> \| FN_CALL.
PIPE' -> .

FN_CALL -> LABEL ARGS.
ARGS -> ( ARG_LIST ).
ARGS -> .
ARG_LIST -> EXPR ARG_LIST'.
ARG_LIST' -> , EXPR.

PATTERN -> LABEL.
PATTERN -> ( PAT_LIST ).
PAT_LIST -> LABEL PAT_LIST'.
PAT_LIST' -> , LABEL.
PAT_LIST' -> .

REF -> LABEL REF_PRIME.
REF_PRIME -> \. LABEL.
REF_PRIME -> .

LITERAL_EXPR -> INTEGER_LITERAL.
LITERAL_EXPR -> STRING_LITERAL.

# Terminals
INTEGER_LITERAL -> 1234.
STRING_LITERAL -> "str".
LABEL -> id.
```
