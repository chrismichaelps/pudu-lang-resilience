---
type: module
path: "@root/tools/Mutate.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
tags: [module, tool]
---

# Mutate (tool)

## Purpose

Mutation testing: apply one operator change at a time to source files, run the checks and the suites, and report survivors.

## Interface

`pudu run tools/Mutate.pudu [--file <path> | --domain] [--every <n>] [--threshold <percent>] [--dry-run]`,
with `PUDU_BIN` naming the compiler (default `pudu`).

## Algorithm

1. Mutants are operator swaps at code positions outside strings, comments, and imports; comparisons must be spaced so type brackets are not mutated.
2. A mutant that fails `pudu check` is invalid; one whose suites still pass survived.
3. `--domain` limits the run to the pure layer; `--every n` samples; `--threshold` fails the run below a score.

## Negative Logic (Prohibited Paths)

- Every mutated file is restored before the next mutant, whatever the verdict.

## Grill Log

- **Q:** Why mutate only the domain in pull requests?
  **A:** It holds the arithmetic and state machines where a single-point change is most likely to go unnoticed, and it runs in minutes. _Rejected:_ the full tree on every pull request.

## Referenced by

[[src/_MOC]] · [[architecture/TESTING]]
