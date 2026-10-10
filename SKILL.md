---
name: skill-selection
description: >
  Dynamic skill orchestrator and environment lock. Activate when the user runs
  /skill-selection, asks to select/lock/filter skills, finalizes an
  implementation plan before coding, or when .skill-manifest.json exists in the
  workspace root. Scans available skills (in-context registry first, disk
  fallback), classifies each as PROTECTED INFRASTRUCTURE (memory, reasoning,
  code graphs, MCPs, output gates) or SELECTABLE PROJECT SKILL (UI themes,
  testing methodologies, framework rules), locks the minimum set needed, and
  persists a native standing rule so new sessions auto-enforce the lock.
argument-hint: "[select|add <name>|drop <name>|reset|status]"
license: MIT
---

# Skill Selection (v1.1)

Dynamic environment orchestrator. Filters the active skill set after planning and before code execution. Contains **zero hardcoded skill names**.

---

## Sub-Commands & Pre-Check

| Command | Action |
|---------|--------|
| `/skill-selection` or `/skill-selection select` | Full workflow: plan check → discover → classify → select → lock |
| `/skill-selection add <name>` | Move a suppressed/ambiguous skill into `[ACTIVE]` and update lock files |
| `/skill-selection drop <name>` | Move an `[ACTIVE]` project skill into `[SUPPRESSED]` (refused for `[PROTECTED]`) |
| `/skill-selection status` | Display current manifest from memory or `.skill-manifest.json` |
| `/skill-selection reset` | Clear suppression directive and delete `.skill-manifest.json` + standing lock rule |

### Trivial-Task Bypass
If the user did NOT explicitly type `/skill-selection` and the request is a trivial micro-edit (fixing a typo, renaming a symbol, changing a single value, answering a quick question), **skip this skill entirely** and execute the task directly. Run the full workflow only for multi-file features, new builds, monorepo context switches, or explicit `/skill-selection` invocations.

---

## Phase 1: Locate the Plan

Verify you know:
1. **Target** — What is being built or modified (web frontend, backend API, ML pipeline, mobile app, infra, etc.).
2. **Tech Stack** — Languages, frameworks, databases, and external APIs.
3. **Execution Mode** — Production engineering (rigor, tests, reviews) vs. Rapid prototype / Hackathon (speed, minimal boilerplate).

Search order:
1. Current conversation context (prior `/grill-me`, `/plan`, or user prompt).
2. Workspace plan files (`PLAN.md`, `TODO.md`, `DESIGN.md`, or task artifacts).
3. If neither exists, stop and ask:
   > "Briefly describe what we are building and the target stack (or run `/grill-me` / `/plan` first) so I can lock the right skills."

---

## Phase 2: Zero-Latency Skill Discovery

Build a complete inventory of installed **skills**, **plugins**, and **MCP servers**.

### Step 2.1: Read In-Context System Registry First (0 Tool Calls)
Before touching the filesystem, inspect your own system prompt context:
- If your harness injects an available `<skills>` list, `<mcp_servers>` list, or loaded rules catalog into the system prompt, **extract the complete inventory directly from memory**.
- Do NOT run shell commands or file reads to re-scan directories that are already indexed in your system prompt.

### Step 2.2: Filesystem Fallback (Only If Not in System Context)
Only if your platform does not inject a skill catalog into the system prompt, scan the existing paths for your current harness:
- **Antigravity / Gemini CLI:** `~/.gemini/config/skills/*/SKILL.md`, `~/.gemini/config/plugins/*/skills/*/SKILL.md`, `.agents/skills/*/SKILL.md`
- **Claude Code:** `~/.claude/commands/*.md`, `.claude/commands/*.md`, `CLAUDE.md`
- **Cursor:** `~/.cursor/rules/*.mdc`, `.cursor/rules/*.mdc`, `.cursorrules`
- **Windsurf:** `~/.windsurf/rules/*.md`, `.windsurf/rules/*.md`, `.windsurfrules`

Record each item's **name** and **description**.

---

## Phase 3: Classify Every Item

Check for `.skill-selection.json` in the workspace root or global config first:
```json
{
  "always_protected": ["custom-memory-skill"],
  "always_suppressed": ["deprecated-theme"]
}
```
Manual overrides in `.skill-selection.json` take **absolute precedence**.

Otherwise, classify every discovered item into **INFRASTRUCTURE** or **PROJECT** based on its description:

### 1. INFRASTRUCTURE (`[PROTECTED]` — Never Suppressed)
Enhances how the agent thinks, remembers, reasons, or navigates across ALL projects. Match if it provides:
- **Reasoning & Thinking** (chain-of-thought, sequential thinking, reflection)
- **Memory & Context** (session memory, context recall, knowledge graphs)
- **Code Intelligence** (AST graphs, impact analysis, dependency mapping, repo indexing)
- **Output Quality Gates** (anti-truncation, full output enforcement, banning `// TODO` placeholders)
- **Agent Orchestration & Debugging** (subagent dispatching, task tracking, systematic debugging process, harness tool adapters, `skill-selection` itself)
- **Live Research & MCP Servers** (ALL MCP servers and live doc fetchers are protected by default)

*Rule:* If suppressing this item makes the agent cognitively weaker or blinder on any arbitrary codebase, it is **INFRASTRUCTURE**.

### 2. PROJECT (`[ACTIVE]` or `[SUPPRESSED]` — Context-Dependent)
Directs *what* gets built or *how* code/assets are styled, structured, or tested. Match if it prescribes:
- **Visual Design & UI Themes** (design systems, CSS frameworks, color palettes, brutalist/minimalist aesthetics)
- **Framework / Language Idioms** (React, Next.js, Flutter, Android, Django, FastAPI conventions)
- **Testing & Verification Dogma** (strict TDD, Playwright/Cypress E2E mandates, git-worktree mandates)
- **Coding Style Philosophy** (lazy/minimal code like `ponytail` vs. strict enterprise clean architecture)
- **Domain Asset Generators** (slides, pitch decks, brandkits, banners, social media graphics, prose humanizers)

*Rule:* If a skill is helpful for some tasks (e.g., frontend UI) but irrelevant or counterproductive for others (e.g., backend database migration or rapid sprint), it is **PROJECT**.

### 3. AMBIGUOUS (`[AMBIGUOUS]`)
If a skill's description is unclear, default it to `[SUPPRESSED]`, flag it under `[AMBIGUOUS]`, and let the user decide.

---

## Phase 4: Select, Confirm, and Lock

### Selection Rules
1. **One Skill Per Domain:** Never activate two competing skills in the same domain (e.g., never activate two competing UI aesthetics or `ponytail` alongside strict `test-driven-development`).
2. **Minimum Sufficient Set:** Activate a project skill ONLY if the current plan directly requires its domain.
3. **Context Adaptation:**
   - *Hackathon / Prototype:* Favor speed/minimalism (`ponytail`); suppress strict TDD, git worktrees, and E2E test suites.
   - *Production / Enterprise:* Favor TDD, code review, and verification; suppress shortcut/minimalist modes.
   - *Monorepo Backend Task:* Suppress all frontend CSS/UI/animation skills.
   - *Monorepo Frontend Task:* Activate the single best UI skill; suppress backend/mobile-only skills.

### Present the Manifest
Output the manifest using this exact structure:

```
╔═══════════════════════════════════════════════════════════╗
║            SKILL MANIFEST — [Project Name]                ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  [PROTECTED] Infrastructure — always active:              ║
║    • [name] — [concise capability]                        ║
║    • [MCP: name] — [concise capability]                   ║
║                                                           ║
║  [ACTIVE] Selected for this build:                        ║
║    • [name] — [why selected for THIS plan]                ║
║                                                           ║
║  [SUPPRESSED] Silenced for this session:                  ║
║    • [name] — [reason / which active skill covers it]     ║
║                                                           ║
║  [AMBIGUOUS] Defaulted to suppressed (/skill-selection add)║
║    • [name] — [why uncertain]                             ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║  Total: X protected | Y active | Z suppressed             ║
╚═══════════════════════════════════════════════════════════╝
```

Ask:
> "Say **go** to lock this manifest, or use `/skill-selection add <name>` / `drop <name>` to adjust."

### Issue the Lock & Persist Across Sessions (On Approval)

Once approved:

1. **Emit the In-Session Directive:**
   > **SKILL MANIFEST LOCKED.**
   > - **ACTIVE & PROTECTED:** Authorized for use.
   > - **SUPPRESSED:** Do NOT read, invoke, reference, or apply any rules, styles, or workflows from suppressed skills. Even if a prompt matches a suppressed skill's trigger, handle it using only `[ACTIVE]` skills and base capabilities.

2. **Write `.skill-manifest.json` in the workspace root:**
   ```json
   {
     "version": "1.1",
     "project": "[Project Name]",
     "updated_at": "[ISO-8601]",
     "protected": ["..."],
     "active": ["..."],
     "suppressed": ["..."]
   }
   ```

3. **Write a Native Standing Rule File (For True Cross-Session Persistence):**
   Because AI platforms do not auto-load `.skill-manifest.json` on new chats, write a concise standing rule file to the platform's native auto-loaded rule path in the workspace:
   - **Antigravity / Gemini CLI:** `.agents/rules/skill-lock.md`
   - **Cursor:** `.cursor/rules/skill-lock.mdc` (with `alwaysApply: true` frontmatter)
   - **Claude Code:** `.claude/rules/skill-lock.md` (or append a demarcated `<!-- SKILL-LOCK -->` block in `CLAUDE.md`)
   - **Windsurf:** `.windsurf/rules/skill-lock.md`

   **Contents of the standing rule file:**
   ```markdown
   ---
   trigger: always_on
   description: Active skill-selection lock for [Project Name]
   ---
   # Active Skill Lock ([Project Name])
   - **ACTIVE PROJECT SKILLS:** [comma-separated list]
   - **PROTECTED INFRASTRUCTURE:** [comma-separated list]
   - **SUPPRESSED SKILLS (DO NOT READ OR INVOKE):** [comma-separated list]
   To modify or clear this lock, run `/skill-selection add <name>`, `/skill-selection drop <name>`, or `/skill-selection reset`.
   ```

---

## Safety Invariants

1. **Never suppress `[PROTECTED]` infrastructure** via `/skill-selection drop`. Refuse and explain that only manual `.skill-selection.json` (`always_suppressed`) can override infrastructure protection.
2. **Never suppress `skill-selection` itself.**
3. **Never hallucinate skills** that were not found in Phase 2.
4. **Clean Reset:** When `/skill-selection reset` runs, delete both `.skill-manifest.json` and the generated `skill-lock` standing rule file so the workspace returns to a 100% clean state.
