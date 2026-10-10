<div align="center">

# skill-selection

**Stop your AI agent from fighting itself.**

A dynamic skill orchestrator that scans your environment, classifies each skill<br>
as infrastructure or project-specific, and locks a minimal manifest — so the agent<br>
uses only what the current project needs.

**Zero hardcoded skill names. Fully dynamic. Works on any platform.**

[Why This Exists](#the-problem) · [Installation](#installation) · [Usage](#usage) · [How It Works](#how-it-works) · [Configuration](#configuration) · [FAQ](#faq)

</div>

---

## The Problem

You install 20+ skills to cover design, testing, debugging, mobile, backend, and AI/ML. Then you start a project and your AI agent:

- **Applies 5 competing CSS philosophies** — brutalist grids mixed with minimalist cards mixed with glassmorphism
- **Writes unit tests during a hackathon** — burning 3 hours on test scaffolding during a 24-hour prototype sprint
- **Loads irrelevant skill instructions** — consuming thousands of context window tokens on skills that have nothing to do with the current task
- **Produces Frankenstein output** — inconsistent code that tries to satisfy every installed skill simultaneously

This is **prompt pollution** — the #1 quality killer in modern agentic coding environments.

---

## How `skill-selection` Differs from Other Orchestrators

Most existing "skill orchestrators" are **task routers** — they break a prompt into subtasks and route Step 1 to Skill A and Step 2 to Skill B. They still leave all 30+ skills active in your environment to collide with each other.

**They orchestrate execution. `skill-selection` orchestrates the environment.**

| Feature | Traditional Task Orchestrators | `skill-selection` |
|---|---|---|
| **Core Question** | *"Which skill handles step 3?"* | *"Which skills should even exist in this session?"* |
| **Competing UI / Style Skills** | Left active (causes CSS bleed) | **Hard-suppressed** (1 skill per domain rule) |
| **Infrastructure Protection** | Treats all skills the same | **Auto-protects** memory, code graphs, MCPs & reasoning |
| **Token Footprint** | High (loads multiple playbooks) | **Minimal** (silences unused skills upfront) |
| **Hardcoded Catalog?** | Often tied to a specific skill repo | **Zero hardcoded names** (scans your live disk) |

---

## The Solution

`skill-selection` creates a hard skill lock after planning and before code execution begins:

```
  Your Plan ──► /skill-selection ──► Locked Manifest ──► Focused Execution
```

It scans every installed skill on your machine, classifies each one, activates only what the project needs, and suppresses everything else.

---

## How It Works

### The Four Phases

```
Phase 1: PLAN        Read the implementation plan or project description
                     ↓
Phase 2: DISCOVER    Scan all installed skills, plugins, and MCP servers
                     ↓
Phase 3: CLASSIFY    Sort each into INFRASTRUCTURE or PROJECT
                     ↓
Phase 4: LOCK        Select minimum necessary, suppress the rest
```

### The Two Categories (The Protected Layer)

Not all skills are equal. Every skill in your environment falls into one of two categories:

| | Infrastructure | Project |
|---|---|---|
| **Purpose** | Makes the agent *think, remember, and navigate* | Directs *what gets built and how it looks* |
| **Examples** | Memory enhancers, reasoning engines, code graphs, output enforcement, MCP servers | UI themes, testing methodologies (TDD), framework conventions, slide/brand generators |
| **Scope** | Needed on **every** project | Needed on **some** projects, harmful on others |
| **Suppressible?** | **Never — auto-protected** | **Yes — selected per project** |

**Infrastructure skills are never suppressed.** This guarantees that running `/skill-selection` will never accidentally disable your memory enhancer, codebase knowledge graph, or reasoning engine.

### Example Output

```
╔═══════════════════════════════════════════════════════════╗
║         SKILL MANIFEST — AI Voice Assistant               ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  [PROTECTED] Infrastructure — always active:              ║
║    • sequential-thinking — Deep reasoning for API logic   ║
║    • code-review-graph — Codebase navigation              ║
║    • full-output-enforcement — Prevents placeholder code  ║
║                                                           ║
║  [ACTIVE] Selected for this build:                        ║
║    • ui-ux-pro-max — Dashboard design system              ║
║    • ponytail — Minimal code for hackathon speed          ║
║                                                           ║
║  [SUPPRESSED] Silenced for this session:                  ║
║    • industrial-brutalist-ui — Wrong aesthetic            ║
║    • gpt-taste — Competing CSS philosophy                 ║
║    • test-driven-development — Hackathon, no TDD          ║
║    • (17 more suppressed...)                              ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║  Total: 3 protected | 2 active | 20 suppressed            ║
╚═══════════════════════════════════════════════════════════╝
```

The same skill can be active in one project and suppressed in another — selection adapts dynamically to your project context:

| Skill | 24h Hackathon | Production SaaS |
|---|:---:|:---:|
| `ponytail` (minimal/lazy code) | Active | Suppressed |
| `test-driven-development` | Suppressed | Active |
| `verification-before-completion` | Suppressed | Active |
| `ui-ux-pro-max` | Active | Active |

---

## Installation

Choose your coding platform below and copy-paste the commands for your operating system.

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

*(Alternatively, append the contents to your root `CLAUDE.md` file: `cat skill-selection/SKILL.md >> CLAUDE.md`)*

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

Copy `SKILL.md` into whatever directory your AI coding assistant scans for skills, rules, or custom instructions (`AGENTS.md`, `.agents/skills/`, etc.). It is a single self-contained file with zero external dependencies.

---

## Usage

### Step 1: Describe your project or lock your plan

```text
I'm building a real-time AI voice assistant with a Next.js dashboard
and Supabase backend. This is for a 24-hour hackathon.
```

*(Or use `/grill-me` or `/plan` first to finalize your architecture).*

### Step 2: Run skill-selection

```text
/skill-selection
```

The agent will scan your installed skills, classify them into Infrastructure vs. Project, and present the manifest for your approval.

### Step 3: Confirm and build

Review the manifest. Reply **go** to lock it, or customize it on the fly:

```text
/skill-selection add slides      # Add pitch deck generation mid-sprint
/skill-selection drop ponytail   # Switch from minimal mode to thorough mode
```

### Command Reference

| Command | Action |
|---|---|
| `/skill-selection` | Run full workflow: discover, classify, select, lock |
| `/skill-selection add <name>` | Add a suppressed skill to the active manifest |
| `/skill-selection drop <name>` | Remove an active project skill (blocked for protected infrastructure) |
| `/skill-selection status` | Display the current active manifest |
| `/skill-selection reset` | Clear the lock and restore all installed skills |

---

## Configuration

### Permanent Overrides: `.skill-selection.json`

Create `.skill-selection.json` in your workspace root or global config directory to define permanent classification rules:

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

| Field | Behavior |
|---|---|
| `always_protected` | Treated as infrastructure — never suppressed on any project |
| `always_suppressed` | Always silenced — never activated even if it matches the project |

Manual overrides take absolute precedence over heuristic classification.

### Workspace Persistence: `.skill-manifest.json`

Once locked, the active manifest is saved to `.skill-manifest.json` in your project root so it persists across sessions. Commit `.skill-manifest.json` to git if you want teammates to share the exact same skill lock.

---

## Platform Effectiveness & Token Savings

| Platform | Suppression Mechanism | Token Savings | Conflict Prevention |
|---|---|---|---|
| **Antigravity / Gemini CLI** | Progressive disclosure — suppressed skills are never read or loaded into context | **10,000 – 40,000 tokens saved/session** | Near system-level isolation |
| **Claude Code** | Prompt-level suppression directive + subagent scoping | Moderate | High |
| **Cursor / Windsurf** | Prompt-level suppression over pre-loaded `.mdc` rules | Minimal (if rules are `alwaysApply`) | High (stops conflicting rule execution) |

---

## Safety Guarantees

1. **Infrastructure is untouchable.** Memory enhancers, reasoning engines, code graphs, MCP servers, and output quality gates are auto-protected.
2. **Confirmation before lock.** Nothing is silenced without showing you the manifest and getting your confirmation first.
3. **Zero hardcoded skill names.** Classification reads actual skill descriptions dynamically.
4. **Trivial-task bypass.** Won't waste your time generating a manifest for a 10-second typo or color fix unless explicitly invoked.
5. **Instant reset.** `/skill-selection reset` restores default behavior immediately.
6. **Self-preserving.** `skill-selection` can never suppress or lock itself out.

---

## Examples

Explore complete sample manifests in [`examples/`](./examples):

- [**Hackathon Manifest**](./examples/hackathon-manifest.md) — 24-hour AI voice app sprint (4 protected, 2 active, 21 suppressed)
- [**Production SaaS Manifest**](./examples/production-manifest.md) — Multi-tenant analytics app (6 protected, 5 active, 10 suppressed)

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
