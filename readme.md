# Exclaim's Language Specification

This repo contains a somewhat formal specification of exclaim's language/grammar, quirks, and compiler.  
  
The language spec evolves alongside the compiler, [exclaim](https://github.com/Jakob-Strobl/exclaim). At times, this spec may be ahead or behind the compiler.

## Version: Pre-release 3

The language is in the early stages and untested, so it's subject to many breaking changes.  

### Reading the Spec

Inside the ```spec/``` folder, each file covers a section of the language.

**[examples.md](spec/examples.md)**: Simple use cases of the language used as a basis for development. Some examples display equivalent ways to produce the same output.  

**[grammar.md](spec/grammar.md)**: Grammar spec with accompanying explanations and thoughts. Currently only includes syntactic definitions, does not cover semantics.  

**[lexer.md](spec/lexer.md)**: Token definitions and lexical analysis information.  

**[grammaphone.md](spec/grammaphone.md)**: Grammar used with my fork of the tool [grammaphone](https://github.com/Jakob-Strobl/grammophone).
