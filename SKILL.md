# Documentation Generator Skill

## When to use this skill
Use this skill when the user asks to document a function, class, script, or
codebase that currently has little or no documentation. Triggers include:
"document this", "add docstrings", "write documentation for this function",
"explain what this code does", or "add comments to this".

## What this skill does
Turns undocumented or poorly documented code into clearly documented code by
adding:
1. A short docstring/comment block explaining what the function/class does
2. A description of each parameter (name, type, purpose)
3. A description of the return value (type and meaning)
4. Any important edge cases, exceptions raised, or side effects
5. One short usage example, when it meaningfully helps understanding

## How to do it well

1. **Read the code fully before writing anything.** Do not guess at
   behavior, trace the actual logic, including conditionals, loops, and
   error handling.
2. **Match the language's documentation convention.** Use docstrings for
   Python (Google or NumPy style), JSDoc for JavaScript/TypeScript, Javadoc
   for Java, etc. Never invent a custom format if the language has a
   standard one.
3. **Be precise about types.** State the exact expected type for every
   parameter and return value, not just "a string" when it's actually
   "a non-empty string matching an email pattern."
4. **Document behavior, not implementation.** Explain *what* the function
   does and *why*, not a line-by-line narration of *how* the code executes.
5. **Call out side effects.** If the function mutates input, writes to
   disk, makes a network call, or raises exceptions, say so explicitly.
6. **Keep it concise.** One to three sentences for the summary. Don't pad
   with restating the function name in sentence form.
7. **Add a usage example only when it adds real clarity** (e.g. non-obvious
   parameters, multiple valid call patterns). Skip it for trivial functions.

## What NOT to do
- Don't document obvious code with no complexity for the sake of coverage
  ("this function returns x" when the function is `def get_x(): return x`)
- Don't change the code's actual logic while documenting it
- Don't use vague type descriptions ("some object", "a value")
- Don't skip documenting error/exception behavior if the function can fail
- Don't write documentation longer than the function itself for simple cases

## Reference examples
See `examples/good-example.md` and `examples/bad-example.md` for a direct
comparison of strong vs. weak documentation on the same function.
