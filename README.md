# Documentation Generator Skill

A skill that teaches an AI coding agent (Claude Code, Goose, or similar
agent harnesses) how to write clear, precise, convention-matching
documentation for undocumented code.

## Why this skill exists
Most codebases have functions with little or no documentation. Writing good
docs is a skill in itself, it's easy to either write nothing useful ("this
function does the thing") or write documentation so verbose it's never
read. This skill encodes a consistent standard for what good documentation
actually looks like, so an AI agent applies it the same way every time.

## How to use
1. Point your AI agent at this skill (e.g. drop it in a `.skills/` or
   `SKILL.md`-aware directory depending on your agent's setup)
2. Ask the agent to "document this function/file/class"
3. The agent will follow the standard in `SKILL.md`: summary, parameters,
   return value, side effects, and an example when useful

## Structure
```
documentation-generator-skill/
├── SKILL.md              - the core instructions
├── README.md              - this file
└── examples/
    ├── good-example.md    - a well-documented function
    └── bad-example.md     - the same function, poorly documented
```

## Built for Hacktoberfest 2026
This skill was built as a live example during the Hacktoberfest Gombe
kickoff session, demonstrating what a real, valid open-source AI
contribution looks like this year: not a pull request for its own sake,
but a reusable tool that makes an AI agent genuinely more useful.

### Companion Guide & Advanced Skills Suite
- **Beginner Guide (This Repo)**: A focused, single-purpose skill demonstrating core `SKILL.md` structure, triggering criteria, and before/after documentation examples.
- **Intermediate / Advanced Suite**: [skills](https://github.com/Adams-404/skills) — A moderately complex multi-skill orchestration suite demonstrating Git worktree isolation, automated runtime video/screen evidence, CI review loops, and prose refinement.
