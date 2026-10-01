# How Skill Installation & Distribution Works

A clear technical explanation of how `npx skills add <username>/<repo>` works under the hood, why it does not require publishing to the npm registry, and how automated discovery, linking, and YAML frontmatter operate.

---

## 1. Does this command require publishing an npm package?

**No.** Running `npx skills add Adams-404/documentation-generator-skill` does not mean an npm package named `Adams-404/documentation-generator-skill` exists on npmjs.org.

Here is what actually happens:

1. **`npx` is just a tool runner**:
   `npx` is a standard Node.js utility that downloads and executes an executable npm package. In this command, the package being executed is [`skills`](https://www.npmjs.com/package/skills), an open-source skill manager published by the developer community (hosted at [skills.sh](https://skills.sh)).

2. **GitHub repository resolution**:
   When the `skills` CLI sees the argument `Adams-404/documentation-generator-skill`, it recognizes the `<owner>/<repo>` pattern and translates it into a Git clone URL:
   `https://github.com/Adams-404/documentation-generator-skill.git`

3. **Automated discovery and linking**:
   The CLI performs the following automated steps on your machine:
   - Clones the target repository into a temporary directory in `/tmp`.
   - Locates any `SKILL.md` file (either at the root or within subdirectories).
   - Reads the YAML frontmatter (`name` and `description`).
   - Automatically detects installed AI coding agents on your system (such as Claude Code, Cursor, Antigravity, or Goose).
   - Copies or symlinks the skill into the appropriate agent's skills directory (e.g. `~/.claude/skills/` or `.agent/skills/`).

---

## 2. Why `npx skills add` instead of `npm install`?

| Tool | Target | Destination | Purpose |
|---|---|---|---|
| **`npm install <package>`** | Application Code | `node_modules/` | Installs libraries you import in JavaScript/TypeScript code (`import x from 'x'`). |
| **`npx skills add <repo>`** | AI Coding Agents | Agent directories (`.claude/skills/`, `.agent/skills/`) | Installs behavioral instructions (`SKILL.md`) and runbooks for AI agents. |

AI coding agents do not scan your project's `node_modules/` for behavioral instructions. They look for `SKILL.md` files in designated configuration directories. The `skills` CLI acts as a specialized package manager specifically for AI agent skills.

---

## 3. Deep Dive: Automated Discovery, Linking, and YAML Frontmatter

Understanding how agents discover and load skills is the single most important concept for anyone building or using agent skills.

### The Problem: The Context Window Tax

An AI coding agent has a limited context window (the number of tokens it can hold in memory at once).

If you have 20 skills installed, and each skill's instruction file is 400 lines long, loading all 20 skills upfront into every conversation turn would consume 15,000 to 20,000 tokens before you even type your first prompt. This causes:
- Slower response times
- High API token costs
- Context saturation and degraded reasoning accuracy

### The Solution: Progressive Disclosure (Two-Tier Loading)

Agent skill systems solve this problem through **Progressive Disclosure**:

```
+-------------------------------------------------------------------+
| Tier 1: Startup (Lightweight Metadata Only)                       |
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

---

## 4. Demystifying YAML Frontmatter

Every `SKILL.md` must begin with a YAML block fenced between triple dashes (`---`). This block is the "index card" that the agent inspects.

```markdown
---
name: documentation-generator
description: Turns undocumented or poorly documented code into clearly documented code with standard docstrings, parameter types, return values, edge cases, and examples. Use when asked to "document this", "add docstrings", "explain what this code does", or "add comments".
---
```

### Key Frontmatter Fields

1. **`name`** (Required):
   - A unique kebab-case slug identifying the skill (e.g. `documentation-generator`, `multi-agent-orchestrator`).
   - Serves as the manual slash command in agent chat interfaces (e.g. typing `/documentation-generator`).

2. **`description`** (Required & Most Critical):
   - This field is **the semantic trigger** for the agent.
   - When you type a prompt, the agent's system prompt includes a list of available skill names and their descriptions. The model evaluates whether your request falls within the scope defined in `description`.
   - If the description is vague (e.g. `description: Helps with code`), the agent will either never trigger it or trigger it constantly by mistake (false positives).
   - If the description is trigger-focused (e.g. `Use when asked to document functions, add docstrings, or explain parameters`), the agent triggers it accurately and autonomously.

### The Frontmatter Writing Formula

A high-performing frontmatter description contains three components:
1. **Action summary**: What concrete capability the skill provides.
2. **Explicit trigger keywords**: Specific verbs and phrases users typically say (`"document this"`, `"write docstrings"`, `"explain parameters"`).
3. **Negative boundaries (when needed)**: Explicitly state when NOT to trigger to prevent accidental invocations (e.g. `Do not use for generating new code from scratch`).

---

## 5. How Linking Works Under the Hood

When you run `npx skills add`, how does the tool attach the skill to your agents?

### Step 1: Agent Directory Detection
The CLI scans standard filesystem locations to detect which agent harnesses are present:
- **Claude Code**: Checks for `./.claude/skills/` (project) or `~/.claude/skills/` (global).
- **Cursor**: Checks for `./.cursor/skills/` or `.cursorrules`.
- **Antigravity / Agent CLI**: Checks for `./.agent/skills/` or `~/.gemini/config/skills/`.
- **Goose / Custom**: Checks for `~/.config/goose/skills/` or generic `.skills/` directories.

### Step 2: Symbolic Linking vs. Copying
- **Default (Symlinking)**: By default, the CLI creates a symbolic link (`ln -s`) from the cloned repository to the agent's skills directory.
  - *Advantage*: If you edit the skill's source files in your local project repository, your agent immediately receives the updates without reinstalling.
- **Copying (`--copy` flag)**: If `--copy` is passed, the CLI makes an independent static copy of the files into the agent folder.

### Step 3: Lockfile Tracking
The CLI writes to a local `skills-lock.json` file recording:
- The remote repository source (`Adams-404/documentation-generator-skill`)
- The Git commit SHA installed
- The installed skill names and destination paths

This allows you to run `npx skills update` in the future to automatically pull the latest changes, or `npx skills remove <skill-name>` to cleanly unlink the files.

---

## 6. How to Make Your Own Skills Installable (3 Steps)

You can make any custom skill installable by anyone worldwide in three steps:

### Step 1: Add a `SKILL.md` with Frontmatter
At the root of your project (or in a subfolder), create a `SKILL.md` with a standard YAML header:

```markdown
---
name: my-custom-skill
description: Plain-English explanation of what the skill does and exact trigger conditions for when the agent should invoke it.
---

# Instructions
Step-by-step guidance for the agent...
```

### Step 2: Push to a Public GitHub Repository
Push your project to GitHub and ensure the repository visibility is set to **Public**.

### Step 3: Install from Anywhere
Anyone can now install your skill into their local AI coding agent by running:

```bash
npx skills add <your-github-username>/<your-repo-name>
```

---

## 7. Private Repositories & Local Development

Because the `skills` CLI clones via public HTTPS by default, running `npx skills add <user>/<private-repo>` on a private repository without active HTTPS credentials will fail.

For private repositories or local testing, you have two options:

### Option A: Local Directory Path
Install directly from a local checkout on your machine:
```bash
npx skills add /path/to/my-skill-folder
```

### Option B: SSH Git URL
Use your local GitHub SSH keys to install from a private repository:
```bash
npx skills add git@github.com:Adams-404/documentation-generator-skill.git
```
