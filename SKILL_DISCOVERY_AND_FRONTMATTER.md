# Skill Discovery, Linking & YAML Frontmatter

A dedicated technical guide explaining how AI coding agents discover skills, how progressive disclosure prevents context window bloat, and why YAML frontmatter is the fundamental trigger mechanism for agent skills.

---

## 1. The Core Problem: The Context Window Tax

An AI coding agent has a limited context window (the active token memory it can process in a conversation).

If an agent loaded 20 installed skills upfront into every conversation turn, and each skill contained a 400-line markdown instruction file, that would consume 15,000 to 20,000 tokens before you even type your first prompt.

This monolithic approach causes three severe problems:
1. **High Token Costs**: You pay for thousands of tokens on every prompt, even when asking simple questions.
2. **Slow Latency**: Processing giant prompt headers slows down model time-to-first-token.
3. **Context Saturation & Hallucination**: Flooding the prompt with dozens of unrelated instructions dilutes model attention and degrades reasoning accuracy.

---

## 2. The Solution: Progressive Disclosure (Two-Tier Loading)

Modern AI agent architectures solve this using **Progressive Disclosure** (loading information only when needed):

```
+-------------------------------------------------------------------+
| Tier 1: Startup & Indexing (Lightweight Metadata Only)            |
| The agent reads ONLY the YAML frontmatter (name & description).  |
| Cost: ~30 tokens per skill (Total for 20 skills: ~600 tokens).   |
+-------------------------------------------------------------------+
                                  |
            User sends prompt: "Document this function"
                                  |
                                  v
+-------------------------------------------------------------------+
| Tier 2: On-Demand Execution (Full Body Injection)                 |
| The LLM matches prompt semantics to the frontmatter description. |
| The agent dynamically loads the full SKILL.md into context.       |
| Cost: Incurred ONLY when the skill is actually needed.            |
+-------------------------------------------------------------------+
```

By reading only YAML frontmatter at startup, the agent keeps 99% of its context window clean for your project code.

---

## 3. Demystifying YAML Frontmatter

Every `SKILL.md` must begin with a YAML block fenced between triple dashes (`---`) at the top of the file. This block is the "index card" that the agent inspects.

```markdown
---
name: documentation-generator
description: Turns undocumented or poorly documented code into clearly documented code with standard docstrings, parameter types, return values, edge cases, and examples. Use when asked to "document this", "add docstrings", "explain what this code does", or "add comments".
---
```

### The Key Fields

#### 1. `name` (Required)
- A unique kebab-case slug identifying the skill (e.g. `documentation-generator`, `multi-agent-orchestrator`).
- Defines the manual slash command in agent chat interfaces (e.g. typing `/documentation-generator`).

#### 2. `description` (Required & Most Critical)
- This field is **the semantic trigger** for the agent.
- When you type a prompt, the agent's system prompt includes a list of available skill names and descriptions. The model evaluates whether your request falls within the scope defined in `description`.
- If the description is vague (e.g. `description: Helps with code`), the agent will either never trigger it or trigger it constantly by mistake (false positives).
- If the description is trigger-focused (e.g. `Use when asked to document functions, add docstrings, or explain parameters`), the agent triggers it accurately and autonomously.

---

## 4. The Frontmatter Writing Formula

A high-performing frontmatter description contains three components:

1. **Action Summary**: What concrete capability the skill provides.
   *Example*: "Turns undocumented or poorly documented code into clearly documented code."
2. **Explicit Trigger Phrases**: The actual verbs, words, and commands users say.
   *Example*: `Use when asked to "document this", "add docstrings", "write documentation", or "add comments".`
3. **Negative Boundaries**: Explicitly state when NOT to trigger to prevent false invocations.
   *Example*: "Do not use for generating new application code from scratch."

---

## 5. How Automated Linking Works Under the Hood

When you run `npx skills add <username>/<repo>`, how does the tool link the skill into your agents?

### Step 1: Agent Directory Detection
The CLI scans standard filesystem locations to detect which agent harnesses are present:
- **Claude Code**: Checks for `./.claude/skills/` (project) or `~/.claude/skills/` (global).
- **Cursor**: Checks for `./.cursor/skills/` or `.cursorrules`.
- **Antigravity / Agent CLI**: Checks for `./.agent/skills/` or `~/.gemini/config/skills/`.
- **Goose / Custom**: Checks for `~/.config/goose/skills/` or generic `.skills/` directories.

### Step 2: Symbolic Linking vs. Copying
- **Default (Symlinking)**: By default, the CLI creates a symbolic link (`ln -s`) from the cloned repository to the agent's skills directory.
  - *Advantage*: If you edit the skill's source files in your local repository, your agent immediately receives the updates without reinstalling.
- **Copying (`--copy` flag)**: If `--copy` is passed, the CLI makes an independent static copy of the files into the agent folder.

### Step 3: Lockfile Tracking
The CLI writes to a local `skills-lock.json` file recording:
- The remote repository source (`Adams-404/documentation-generator-skill`)
- The Git commit SHA installed
- The installed skill names and destination paths

This allows you to run `npx skills update` in the future to automatically pull the latest changes, or `npx skills remove <skill-name>` to cleanly unlink the files.

---

## 6. Common Pitfalls for Beginners

1. **Missing or Broken Frontmatter**:
   - The YAML block must start on **line 1, column 1** with `---`.
   - If there is whitespace, a title, or a comment before `---`, the agent parser treats the frontmatter as plain text and fails to index the skill.

2. **Writing Instructions in the Description**:
   - The `description` field is only for deciding *when* to load the skill.
   - Do not write your entire playbook or step-by-step instructions inside `description`. Keep `description` under 4-5 sentences and place all procedural instructions in the Markdown body below `---`.

3. **Vague Trigger Keywords**:
   - If your description only says `Helps developers write better software`, the LLM has no concrete trigger signals and will rarely invoke it. Always include the specific verbs and scenarios users type.
