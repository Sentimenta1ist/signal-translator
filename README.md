# SIGNAL Translator

Three stages of a translator for a small subset of the SIGNAL language (procedure declarations with typed parameters): lexer, recursive-descent parser with a parse tree, and an assembly-like code generator.

Year: 2021-2022 · Language: C++ · Context: university coursework (KPI), "Translator design basics" course, variant 10

## What's inside

| Folder | What it does |
|---|---|
| `lab1/` | Lexical analyzer: reads `input.sig`, prints lexeme table (row, column, code, text), plus id / number / separator tables and lexer errors. Report: `Ivanenko_lab1_report.pdf`, grammar in `gram.png`, automaton in `graph.png` |
| `lab2/` | Lexer + parser + code generator. Prints the parse tree, tables and the generated ASM. Report: `lab2_report.pdf` |
| `rgr/` | Term project ("syntax analyzer"): lexer + parser with parse tree and error listing (no generator). Report: `Ivanenko_rgr_report.pdf` |
| `*/tests/testNN/` | Each case has `input.sig` and `expected.txt` (reference output) |

Source files in each folder: `lexer.*`, `lexeme.*`, `parser.*`, `tree.*`, `Err.*`, `generator.*` (lab2 only), `main.cpp`. Reports are in Ukrainian.

## Interesting bits

- The lexer in `lab2/lexer.cpp` classifies each character into categories (whitespace, digit, letter, delimiter, `(` for comment start, unacceptable) and runs hand-written loops over them; no regular expressions (the assignment forbids them). Keywords get codes from 401 up, delimiters use their ASCII code, numbers are numbered from 500 and identifiers from 1000.
- Comments `(* ... *)` may span lines; the lexer keeps row/column counters in step and reports "Comment not ended" if the end is missing. Errors like "letter after digit" (`1234d`) carry position.
- `lab2/parser.cpp` is recursive descent: one method per grammar rule (`program`, `procedureIdentifier`, ...), every consumed lexeme is attached to a `TreeNode` (`tree.cpp`), and `<empty>` productions appear explicitly in the printed tree.
- `lab2/generator.cpp` walks the parse tree and, per procedure, emits `push ebp / mov ebp, esp`, then `mov <param>, [ebp - offset]` for each parameter, and `ret`. It reports semantic errors: duplicate parameter name, duplicate procedure name, missing base type, attribute other than `complex`.

## Run

Verified with g++ (C++14). The program takes a test folder path **with a trailing slash**, reads `input.sig` from it and writes `generated.txt` there:

```
g++ -std=c++14 lab2/*.cpp -o translator
./translator lab2/tests/test01/
```

`rgr/` builds and runs the same way. With CRLF/trailing whitespace ignored, the produced `generated.txt` matches `expected.txt` for all `rgr` tests and for 6 of 7 `lab2` tests (`test02` differs only in the wording of one error message). `lab1/` does not compile as committed (`lexer.h` lacks the `token` and `all_tokens` declarations that `lexer.cpp` uses); the lexer logic is the same one that is in `lab2/`.

## Note

Written in 2021-2022, before AI coding assistants. Kept as-is for history.
