# How Skill Installation & Distribution Works

A clear technical explanation of how `npx skills add <username>/<repo>` works under the hood, why it does not require publishing to the npm registry, and how to distribute your own custom agent skills.

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

For a detailed technical breakdown of how agents discover skills, why progressive disclosure prevents context bloat, and how to write effective YAML frontmatter triggers, see **[SKILL_DISCOVERY_AND_FRONTMATTER.md](SKILL_DISCOVERY_AND_FRONTMATTER.md)**.

---

## 2. Why `npx skills add` instead of `npm install`?

| Tool | Target | Destination | Purpose |
|---|---|---|---|
| **`npm install <package>`** | Application Code | `node_modules/` | Installs libraries you import in JavaScript/TypeScript code (`import x from 'x'`). |
| **`npx skills add <repo>`** | AI Coding Agents | Agent directories (`.claude/skills/`, `.agent/skills/`) | Installs behavioral instructions (`SKILL.md`) and runbooks for AI agents. |

AI coding agents do not scan your project's `node_modules/` for behavioral instructions. They look for `SKILL.md` files in designated configuration directories. The `skills` CLI acts as a specialized package manager specifically for AI agent skills.

---

## 3. How to Make Your Own Skills Installable (3 Steps)

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

## 4. Private Repositories & Local Development

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
