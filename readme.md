# Exclaim Grammar Specification

This repo contains a somewhat formal specification of exclaim's grammar, quirks, and compiler.  
  
The language spec evolves alongside the compiler, [exclaim](https://github.com/Jakob-Strobl/exclaim). At times, exclaim's spec may be ahead or behind the compiler.

## Version: Pre-release 1

The language is in the early stages and untested, so it's subject to many breaking changes.  

### Reading the Spec

Inside the ```src/``` folder, each file covers a section of the language.

**[examples.md](src/examples.md)**: Simple use cases of the language used as a basis for development. Some examples display equivalent ways to produce the same output.  

**[grammar.md](src/grammar.md)**: Grammar spec with accompanying explanations and thoughts. Currently only includes syntactic definitions, does not cover semantics.  

**[lexer.md](src/lexer.md)**: Token definitions and lexical analysis information.  
