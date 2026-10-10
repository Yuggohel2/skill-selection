<div align="center">

# skill-selection

**Stop your AI agent from fighting itself.**

A zero-latency environment orchestrator that classifies your installed skills<br>
into protected infrastructure vs. project-specific rules, locks the minimum set<br>
needed for your current task, and silences everything else.

**Zero hardcoded skill names. In-memory discovery. Native cross-session persistence.**

[The Problem](#the-problem) · [Why It's Different](#how-skill-selection-differs-from-task-orchestrators) · [Installation](#installation) · [Usage](#usage) · [How It Works](#how-it-works) · [Configuration](#built-for-long-term-daily-use-v12-features) · [FAQ](#faq)

</div>

---

## The Problem

Whether you are working in an **enterprise polyglot monorepo**, shipping as an **indie hacker / vibecoder**, or sprinting in a **24-hour hackathon**, installing 15+ agent skills creates **prompt pollution**:

- **Monorepo & Microservice Context Bleed:** You are refactoring a Python/Go backend service or Terraform infrastructure, but your React, Tailwind, and frontend animation skills keep firing and polluting the context window.
- **Competing Style & Architecture Doctrines:** Multiple UI skills (`ui-ux-pro-max`, `design-taste-frontend`, `minimalist-ui`, `industrial-brutalist-ui`) compete in the same prompt, producing inconsistent "Frankenstein" components.
- **Methodology Collisions (Production vs. Prototype):** Strict TDD, git-worktree, and E2E testing skills fire during rapid prototyping sprints — or conversely, shortcut/minimalist skills fire during critical production PRs.
- **Token & Latency Bloat:** Every unnecessary skill read by the agent burns thousands of context tokens and degrades reasoning accuracy.

---

## How `skill-selection` Differs from Task Orchestrators

Most existing "skill orchestrators" are **task routers** — they decompose a prompt into subtasks and route Step 1 to Skill A and Step 2 to Skill B, while leaving all 30+ skills active in your environment to collide with each other.

**They orchestrate execution. `skill-selection` orchestrates the environment.**

| Feature | Traditional Task Orchestrators | `skill-selection` (v1.2) |
|---|---|---|
| **Core Question** | *"Which skill handles step 3?"* | *"Which skills should even exist in this session?"* |
| **Discovery Latency** | Scans disk files on every run (slow) | **In-memory system prompt inspection first (0 tool calls)** |
| **Competing Domain Skills** | Left active (causes style/rule bleed) | **Hard-suppressed** (strict 1-skill-per-domain lock) |
| **Infrastructure Protection** | Treats all skills identically | **Auto-protects** memory, code graphs, MCPs & reasoning |
| **Cross-Session Persistence** | Lost when opening a new chat | **Writes native standing rules** (`.agents/rules/`, `.cursor/rules/`) |
| **Scope-Shift Protection** | None | **Warns you** if you switch tasks while an old lock suppresses needed skills |
| **Git Tree Hygiene** | Leaves untracked files in `git status` | **Auto-excludes via `.git/info/exclude`** (never touches team `.gitignore`) |

---

## How It Works

```
  Your Plan / Task ──► /skill-selection ──► Locked Manifest + Standing Rule ──► Focused Execution
```

### The Four Phases

```
Phase 1: PLAN        Identify target component, tech stack, and mode (Production vs. Sprint)
                     ↓
Phase 2: DISCOVER    Read in-context system registry first (0 disk I/O); fallback to disk scan
                     ↓
Phase 3: CLASSIFY    Sort every item into PROTECTED INFRASTRUCTURE or PROJECT SKILL
                     ↓
Phase 4: LOCK        Activate 1 skill per needed domain, silence the rest, persist standing rule
```

### The Protected Infrastructure Layer

Not all skills are equal. `skill-selection` separates cognitive infrastructure from project rules:

| | Infrastructure (`[PROTECTED]`) | Project (`[ACTIVE]` / `[SUPPRESSED]`) |
|---|---|---|
| **Purpose** | Enhances how the agent *thinks, remembers, and navigates* | Directs *what gets built, styled, or tested* |
| **Examples** | Memory enhancers, `sequential-thinking`, `code-review-graph`, output quality gates, all MCP servers | UI themes, CSS rules, TDD workflows, framework conventions, slide/brand generators |
| **Scope** | Needed on **every** project | Helpful on **some** tasks, harmful on others |
| **Suppressible?** | **Never — auto-protected** | **Yes — selected dynamically per task** |

**Infrastructure is never suppressed.** Running `/skill-selection` will never disable your codebase knowledge graph, memory enhancer, or MCP servers.

### Example Output

```
╔═══════════════════════════════════════════════════════════╗
║         SKILL MANIFEST — Payment Service API              ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  [PROTECTED] Infrastructure — always active:              ║
║    • sequential-thinking — Multi-step architecture logic  ║
║    • code-review-graph — AST dependency & impact analysis ║
║    • full-output-enforcement — Prevents placeholder code  ║
║    • MCP: github-mcp-server — PR and repository tools     ║
║                                                           ║
║  [ACTIVE] Selected for this build:                        ║
║    • test-driven-development — Production payment logic   ║
║    • verification-before-completion — Pre-merge checks    ║
║                                                           ║
║  [SUPPRESSED] Silenced for this session:                  ║
║    • ui-ux-pro-max — Backend service (no UI in scope)     ║
║    • design-taste-frontend — Backend service (no CSS)     ║
║    • ponytail — Production payment code requires rigor    ║
║    • (18 more suppressed...)                              ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║  Total: 4 protected | 2 active | 21 suppressed            ║
╚═══════════════════════════════════════════════════════════╝
```

Selection adapts dynamically to your context:

| Skill | Enterprise Backend PR | Production SaaS UI | 24h Hackathon / Vibecoding |
|---|:---:|:---:|:---:|
| `test-driven-development` | Active | Active | Suppressed |
| `verification-before-completion` | Active | Active | Suppressed |
| `ui-ux-pro-max` | Suppressed | Active | Active |
| `ponytail` (minimal/shortcut mode) | Suppressed | Suppressed | Active |

---

## Installation

Choose your platform below and run the commands for your OS.

### 1. Google Antigravity / Gemini CLI

**macOS / Linux (Bash):**
```bash
git clone https://github.com/Yuggohel2/skill-selection.git
mkdir -p ~/.gemini/config/skills/skill-selection
cp skill-selection/SKILL.md ~/.gemini/config/skills/skill-selection/SKILL.md
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/Yuggohel2/skill-selection.git
New-Item -ItemType Directory -Path "$HOME\.gemini\config\skills\skill-selection" -Force
Copy-Item "skill-selection\SKILL.md" -Destination "$HOME\.gemini\config\skills\skill-selection\SKILL.md" -Force
```

---

### 2. Claude Code

**macOS / Linux (Bash):**
```bash
git clone https://github.com/Yuggohel2/skill-selection.git
mkdir -p .claude/commands
cp skill-selection/SKILL.md .claude/commands/skill-selection.md
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/Yuggohel2/skill-selection.git
New-Item -ItemType Directory -Path ".claude\commands" -Force
Copy-Item "skill-selection\SKILL.md" -Destination ".claude\commands\skill-selection.md" -Force
```

---

### 3. Cursor

**macOS / Linux (Bash):**
```bash
git clone https://github.com/Yuggohel2/skill-selection.git
mkdir -p .cursor/rules
cp skill-selection/SKILL.md .cursor/rules/skill-selection.mdc
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/Yuggohel2/skill-selection.git
New-Item -ItemType Directory -Path ".cursor\rules" -Force
Copy-Item "skill-selection\SKILL.md" -Destination ".cursor\rules\skill-selection.mdc" -Force
```

---

### 4. Windsurf

**macOS / Linux (Bash):**
```bash
git clone https://github.com/Yuggohel2/skill-selection.git
mkdir -p .windsurf/rules
cp skill-selection/SKILL.md .windsurf/rules/skill-selection.md
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/Yuggohel2/skill-selection.git
New-Item -ItemType Directory -Path ".windsurf\rules" -Force
Copy-Item "skill-selection\SKILL.md" -Destination ".windsurf\rules\skill-selection.md" -Force
```

---

### 5. Any Other Platform

Copy `SKILL.md` into your platform's custom skill or rules directory (`.agents/skills/skill-selection/SKILL.md`, `AGENTS.md`, etc.). It is a single self-contained file with zero external dependencies.

---

## Usage

### Standard Mode (Review Before Locking)
```text
/skill-selection
```
The agent inspects its in-context skill registry, classifies every skill, displays the manifest box, and waits for you to reply **go** before locking.

### Fast Mode (Zero-Turn Lock & Build)
```text
/skill-selection fast
```
Locks the manifest immediately without waiting for a confirmation turn and starts executing your build right away. Ideal once you trust the classifier.

### Mid-Session Adjustments
```text
/skill-selection add slides      # Activate slide generator mid-session
/skill-selection drop ponytail   # Switch back to full production rigor
```

### Command Reference

| Command | Action |
|---|---|
| `/skill-selection` | Full workflow: plan check → discover → classify → review → lock |
| `/skill-selection fast` | Instant lock without waiting for a confirmation turn |
| `/skill-selection add <name>` | Move a suppressed skill into `[ACTIVE]` and update lock files |
| `/skill-selection drop <name>` | Move an `[ACTIVE]` project skill into `[SUPPRESSED]` (blocked for `[PROTECTED]`) |
| `/skill-selection status` | Display the current active manifest |
| `/skill-selection reset` | Clear the lock and delete `.skill-manifest.json` + standing rule file |

---

## Built for Long-Term Daily Use (v1.2 Features)

### 1. Scope-Shift Guardrail (No "Forgotten Lock" Bugs)
What happens if you lock backend skills on Monday, and on Thursday in the same repo you ask the agent to *"build the pricing page UI"*?  
Instead of silently building an unstyled page because UI skills were suppressed 3 days ago, the standing rule triggers a **Scope-Shift Alert**:
```text
[SCOPE SHIFT] You requested frontend UI work, but ui-ux-pro-max is currently suppressed by your active lock. Run '/skill-selection add ui-ux-pro-max' to enable it, or tell me to proceed without it.
```

### 2. Clean `git status` via `.git/info/exclude`
When working in corporate or client repositories, you cannot pollute the shared `.gitignore` with personal AI config files. `skill-selection` automatically appends `.skill-manifest.json` and the standing rule path to `.git/info/exclude` (Git's local-only ignore file), keeping your `git status` 100% clean.

### 3. Overrides Aggressive Third-Party `<EXTREMELY-IMPORTANT>` Tags
Some third-party skill packs wrap their triggers in `<EXTREMELY-IMPORTANT>` tags to force execution on every turn. `skill-selection` stamps its lock as an explicit **User-Level Instruction**, which by platform hierarchy (`User Instructions > Skills`) legally supersedes mandatory tags inside suppressed plugins.

### 4. True Cross-Session Persistence
When locked, `skill-selection` writes both `.skill-manifest.json` and a native auto-loaded workspace rule (`.agents/rules/skill-lock.md`, `.cursor/rules/skill-lock.mdc`, `.claude/rules/skill-lock.md`, or `.windsurf/rules/skill-lock.md`) so new chats enforce your lock automatically from turn one.

### 5. Permanent Overrides: `.skill-selection.json`
Create `.skill-selection.json` in your workspace root or global config directory to enforce permanent classification rules:

```json
{
  "always_protected": [
    "my-company-standards",
    "custom-memory-enhancer"
  ],
  "always_suppressed": [
    "legacy-skill-i-never-use"
  ]
}
```

---

## Platform Effectiveness & Token Savings

| Platform | Suppression Mechanism | Token Savings | Conflict Prevention |
|---|---|---|---|
| **Antigravity / Gemini CLI** | Progressive disclosure + `.agents/rules/skill-lock.md` — suppressed skills are never read into context | **10,000 – 40,000 tokens saved/session** | Near system-level isolation |
| **Claude Code** | Standing rule lock + subagent context scoping | Moderate to High | High |
| **Cursor / Windsurf** | `.cursor/rules/skill-lock.mdc` override directive | Moderate (stops on-demand rule reads) | High (prevents competing rule execution) |

---

## FAQ

**Does `/skill-selection` waste time scanning my hard drive on every run?**  
No. Phase 2 inspects the agent's in-context system prompt registry first (`<skills>` and `<mcp_servers>`), completing discovery in **zero tool calls**. It only falls back to filesystem scanning on platforms that do not expose a skill index in the system prompt.

**Why is `SKILL.md` so much smaller than `README.md`?**  
By design. `README.md` is documentation for humans; `SKILL.md` is the prompt payload read by the LLM. All human-only FAQs and installation guides are kept strictly out of `SKILL.md` so the skill consumes minimal context tokens (~8 KB) when activated.

**When should I NOT use this?**  
For trivial one-line edits (fixing a typo, changing a hex color, renaming a variable). `skill-selection` includes a built-in pre-check that automatically bypasses manifest generation on micro-edits unless you explicitly invoke `/skill-selection`.

---

## Examples

See [`examples/`](./examples) for complete sample manifests:

- [**Production SaaS Manifest**](./examples/production-manifest.md) — Multi-tenant analytics dashboard with TDD, code review, and pre-merge verification
- [**Hackathon Sprint Manifest**](./examples/hackathon-manifest.md) — 24-hour AI voice app sprint with minimal code and maximum speed

---

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## License

Distributed under the MIT License. See [`LICENSE`](./LICENSE) for details.
