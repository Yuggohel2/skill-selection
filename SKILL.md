---
name: skill-selection
description: >
  Dynamic skill orchestrator and environment lock. Activate when the user runs
  /skill-selection, asks to select/lock/filter skills, finalizes an
  implementation plan before coding, or when .skill-manifest.json exists in the
  workspace root. Scans available skills (in-context registry first, disk
  fallback), classifies each as PROTECTED INFRASTRUCTURE (memory, reasoning,
  code graphs, MCPs, output gates) or SELECTABLE PROJECT SKILL (domain-specific
  rules, paradigms, workflows), locks the minimum set needed, keeps git status
  clean via .git/info/exclude, alerts on scope shifts, and persists a native
  standing rule so new sessions auto-enforce the lock.
argument-hint: "[select|fast|add <name>|drop <name>|reset|status]"
license: MIT
---

# Skill Selection

Dynamic environment orchestrator. Filters the active skill set after planning and before execution. Contains **zero hardcoded skill names, frameworks, or domains** — adapts to any software discipline, platform, and installed skill catalog.

---

## Sub-Commands & Pre-Check

| Command | Action |
|---------|--------|
| `/skill-selection` or `/skill-selection select` | Full workflow: plan check → discover → classify → show manifest → confirm & lock |
| `/skill-selection fast` | Zero-turn fast lock: discover → classify → lock immediately (no `go` prompt) → start work |
| `/skill-selection add <name>` | Move a suppressed/ambiguous skill into `[ACTIVE]` and update lock files |
| `/skill-selection drop <name>` | Move an `[ACTIVE]` project skill into `[SUPPRESSED]` (refused for `[PROTECTED]`) |
| `/skill-selection status` | Display current manifest from memory or `.skill-manifest.json` |
| `/skill-selection reset` | Clear suppression directive and delete `.skill-manifest.json` + standing lock rule |

### Trivial-Task Bypass
If the user did NOT explicitly type `/skill-selection` and the request is a trivial micro-edit (fixing a typo, renaming a symbol, changing a single value, answering a quick question), **skip this skill entirely** and execute the task directly. Run the full workflow only for multi-file tasks, new builds, domain switches, or explicit `/skill-selection` invocations.

---

## Phase 1: Understand the Task & Context

Before classifying or selecting skills, verify you understand:
1. **Target Domain & Layer** — What is being built, analyzed, or modified (any software, system, model, infrastructure, or content domain).
2. **Technology Stack** — Languages, runtimes, tools, and external interfaces involved.
3. **Execution Intent** — High-rigor production engineering (strict verification, testing, compliance) vs. rapid prototyping / exploration (speed, minimal ceremony).

Search order:
1. Current conversation context (prior `/grill-me`, `/plan`, or user prompt).
2. Workspace plan or specification files (`PLAN.md`, `TODO.md`, `SPEC.md`, `DESIGN.md`, or task artifacts).
3. If neither exists, stop and ask:
   > "Briefly describe what we are building and your target stack/goal (or run `/grill-me` / `/plan` first) so I can lock the right skills."

---

## Phase 2: Zero-Latency Skill Discovery

Build a complete inventory of all installed **skills**, **plugins**, and **MCP servers**.

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
- **Other Platforms:** Scan standard workspace and user-level skill/rule directories.

Record each discovered item's **name** and **description**.

---

## Phase 3: Classify Every Discovered Item

Check for `.skill-selection.json` in the workspace root or global config first:
```json
{
  "always_protected": ["..."],
  "always_suppressed": ["..."]
}
```
Manual overrides in `.skill-selection.json` take **absolute precedence**.

Otherwise, classify every discovered item into **INFRASTRUCTURE** or **PROJECT** based purely on its semantic description:

### 1. INFRASTRUCTURE (`[PROTECTED]` — Never Suppressed)
A skill or tool is **INFRASTRUCTURE** if it enhances *how the agent thinks, remembers, reasons, researches, or navigates* across **all** projects, regardless of domain or tech stack. Classify as `[PROTECTED]` if it provides:
- **Reasoning & Cognition** — Step-by-step analysis, chain-of-thought, architectural reflection, or problem decomposition.
- **Memory & Context Retention** — Session continuity, context recall, or persistent knowledge management.
- **Codebase Intelligence** — Dependency graphs, AST navigation, symbol indexing, or impact analysis.
- **Output Completeness & Quality Gates** — Preventing truncated outputs, enforcing complete file generation, or banning unfinished placeholders.
- **Agent Orchestration & Debugging Methodology** — Subagent coordination, task tracking, root-cause debugging methodology, platform tool adapters, and `skill-selection` itself.
- **External Connectivity & Research** — All MCP servers, live documentation fetchers, and search tools.

*Universal Test:* If suppressing this item would make the agent cognitively weaker, blinder, or less capable on *every* project, it is **INFRASTRUCTURE**. (Note: meta-rules that blindly force *all* installed skills to activate remain subordinate to the `[SUPPRESSED]` list for domain-specific project skills.)

### 2. PROJECT (`[ACTIVE]` or `[SUPPRESSED]` — Task-Dependent)
A skill is a **PROJECT** skill if it prescribes *what* gets built or *how* specific code, architectures, tests, or assets are produced within a particular domain, stack, or workflow mode. Classify as **PROJECT** if it defines:
- **Language, Framework, or Stack Conventions** — Idioms, patterns, or tooling rules tied to specific languages, runtimes, or libraries.
- **Design, Aesthetic, or Formatting Systems** — Visual styling, layout doctrines, interface conventions, or tone/prose formatting.
- **Verification & Workflow Methodologies** — Specific testing workflows, branch/worktree procedures, or audit checklists.
- **Implementation Philosophy** — Opinionated coding modes (e.g., rapid minimal-code prototyping vs. exhaustive enterprise architecture).
- **Specialized Deliverable Generators** — Skills that produce specific artifact types (presentations, documentation formats, media, infrastructure templates, etc.).

*Universal Test:* If a skill is valuable for *certain* domains, layers, or execution modes, but irrelevant or counterproductive for others, it is **PROJECT**.

### 3. AMBIGUOUS (`[AMBIGUOUS]`)
If an item's description does not clearly indicate whether it is universal cognitive infrastructure or a domain-specific project skill, default it to `[SUPPRESSED]`, list it under `[AMBIGUOUS]`, and let the user decide.

---

## Phase 4: Select, Confirm, and Lock

### Selection Rules
1. **Strict Domain Alignment:** Activate a project skill ONLY if its domain, language, layer, or deliverable type is directly required by the current task plan. Suppress all skills belonging to unrelated domains or layers.
2. **Mutual Exclusion (One Skill Per Role):** If multiple installed project skills compete to govern the same functional role (e.g., two competing styling systems, two conflicting architectural paradigms, or a rapid-shortcut mode vs. a strict-verification mode), select ONLY the single best match for the task and suppress the others.
3. **Intent Matching:** Align active workflow skills with the user's execution intent (activate verification/testing skills for production-grade tasks; suppress heavy ceremony for rapid prototypes/sprints).

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
║    • [name] — [why selected for THIS task]                ║
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

- **Standard Mode (`/skill-selection`):** Ask *"Say **go** to lock this manifest, or use `/skill-selection add <name>` / `drop <name>` to adjust."* (Wait for approval before writing files).
- **Fast Mode (`/skill-selection fast` or when user asks to lock and build immediately):** Skip waiting for `go`. Immediately apply the lock, write the persistence files, and proceed with the user's task.

### Issue the Lock & Persist Across Sessions

Once locked:

1. **Emit the In-Session Directive (Supersedes Third-Party Mandatory Tags):**
   > **SKILL MANIFEST LOCKED.**
   > - **USER-INSTRUCTION PRECEDENCE:** This manifest is an explicit user instruction. By platform hierarchy (User Instructions > Skills), this lock strictly overrides any `<EXTREMELY-IMPORTANT>` or mandatory-invocation tags inside third-party plugins for all skills listed under `[SUPPRESSED]`.
   > - **ACTIVE & PROTECTED:** Authorized for use.
   > - **SUPPRESSED:** Do NOT read, invoke, reference, or apply any rules, patterns, or workflows from suppressed skills.
   > - **SCOPE-SHIFT GUARDRAIL:** If the user later requests work in a domain whose skills are currently `[SUPPRESSED]`, emit a 1-line alert:
   >   `[SCOPE SHIFT] You requested <domain> work, but <skill-name> is currently suppressed by your active lock. Run '/skill-selection add <skill-name>' to enable it, or tell me to proceed without it.`

2. **Write `.skill-manifest.json` in the workspace root:**
   ```json
   {
     "version": "1.2",
     "project": "[Project Name]",
     "updated_at": "[ISO-8601]",
     "protected": ["..."],
     "active": ["..."],
     "suppressed": ["..."]
   }
   ```

3. **Write a Native Standing Rule File (For True Cross-Session Persistence):**
   Write a standing rule file to the platform's native auto-loaded rule path in the workspace:
   - **Antigravity / Gemini CLI:** `.agents/rules/skill-lock.md`
   - **Cursor:** `.cursor/rules/skill-lock.mdc` (with `alwaysApply: true` frontmatter)
   - **Claude Code:** `.claude/rules/skill-lock.md` (or demarcated `<!-- SKILL-LOCK -->` block in `CLAUDE.md`)
   - **Windsurf:** `.windsurf/rules/skill-lock.md`

   **Contents of the standing rule file:**
   ```markdown
   ---
   trigger: always_on
   description: Active skill-selection lock for [Project Name]
   ---
   # Active Skill Lock ([Project Name])
   This is an explicit user-level instruction that overrides `<EXTREMELY-IMPORTANT>` tags in third-party skills for suppressed items.
   - **ACTIVE PROJECT SKILLS:** [comma-separated list]
   - **PROTECTED INFRASTRUCTURE:** [comma-separated list]
   - **SUPPRESSED SKILLS (DO NOT READ OR INVOKE):** [comma-separated list]
   - **SCOPE-SHIFT ALERT:** If the user requests work in a domain covered by a SUPPRESSED skill, output a 1-line warning asking if they want to run `/skill-selection add <skill>` before proceeding.
   To modify or clear this lock, run `/skill-selection add <name>`, `/skill-selection drop <name>`, or `/skill-selection reset`.
   ```

4. **Keep `git status` Clean (`.git/info/exclude`):**
   If `.git/info/exclude` exists in the workspace, append `.skill-manifest.json` and the standing rule path (e.g. `.agents/rules/skill-lock.md`) to `.git/info/exclude` if not already present. This keeps `git status` 100% clean on team and client repositories without modifying the shared `.gitignore`.

---

## Safety Invariants

1. **Never suppress `[PROTECTED]` infrastructure** via `/skill-selection drop`. Refuse and explain that only manual `.skill-selection.json` (`always_suppressed`) can override infrastructure protection.
2. **Never suppress `skill-selection` itself.**
3. **Never hallucinate skills** that were not found in Phase 2.
4. **Clean Reset:** When `/skill-selection reset` runs, delete both `.skill-manifest.json` and the generated `skill-lock` standing rule file so the workspace returns to a 100% clean state.
