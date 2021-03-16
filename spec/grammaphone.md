# Grammaphone

[Grammaphone](https://github.com/mdaines/grammophone) is a great tool made by **mdaines**.  

I forked the repo and added some features (escape characters) to brainstorm grammar ideas.  

The following grammar can be copy and pasted into my fork of [grammaphone](https://github.com/Jakob-Strobl/grammophone). The grammar will be a close approximation to the implemented version. Compared to the grammar defined in [grammar.md](grammar.md), the terminals are trimmed to reduce the number of permutations for better example sentences.  

```
BLOCK -> {{ BLOCK_STMT }}.

BLOCK_STMT -> LET_STMT.
BLOCK_STMT -> RENDER_STMT.
BLOCK_STMT -> WRITE_STMT.

LET_STMT -> let! PATTERN LET_STMT'.
LET_STMT' -> = EXPR.

RENDER_STMT -> render! PATTERN EACH.

EACH -> : EXPR.

WRITE_STMT -> write! EXPR.

EXPR -> REF EXPR'.
EXPR -> LITERALS EXPR'.

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

# Terminals
LITERALS -> 1234 | "str".
LABEL -> id.
```
