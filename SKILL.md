---
name: documentation-generator
description: Writes precise, convention-matching documentation for undocumented or poorly documented functions, classes, scripts, and codebases. Use when asked to document code, add docstrings or comments, explain code behavior, or improve API documentation. Do not use for implementing new behavior or refactoring code unrelated to documentation.
---

# Documentation Generator

Document code that is undocumented, incomplete, or difficult to understand.
Preserve the code's behavior and follow the documentation convention already
used by the language and repository.

## Workflow

1. Read the target code completely before writing documentation. Trace its
    conditionals, loops, error handling, and interactions with other code.
2. Identify the public behavior: purpose, inputs, outputs, errors, side
    effects, mutation, and important constraints.
3. Check nearby documentation and repository conventions. Match the existing
    style instead of introducing a new format.
4. Add or improve documentation without changing runtime behavior.
5. Review the result for factual accuracy, useful specificity, and concise
    wording.

## Document

Include the following when they apply:

- A short summary of what the symbol does and why it exists
- Every parameter's name, exact type, purpose, and meaningful constraints
- The return type and the meaning of the returned value
- Exceptions or error conditions callers may encounter
- Side effects, including mutation, file or network I/O, logging, and state
    changes
- One short usage example when the API or behavior is non-obvious

Describe behavior rather than narrating implementation details. For simple
symbols, keep the documentation shorter than or comparable to the code itself.

## Conventions

Use the standard convention for the language and the repository, for example:

- Python: docstrings in the repository's Google or NumPy style
- JavaScript or TypeScript: JSDoc
- Java: Javadoc
- Other languages: the ecosystem's established documentation format

Use exact types and constraints. Prefer `non-empty string matching ...` over
vague descriptions such as `a string` when the code enforces a narrower
contract.

## Boundaries

- Do not guess about behavior that the code does not establish.
- Do not document obvious statements merely to increase coverage.
- Do not change implementation logic, names, formatting, or tests unless the
  user explicitly requests those changes.
- Do not omit known exceptions or side effects.
- Do not add an example when it would merely repeat a trivial function call.
