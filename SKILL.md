---
name: skill-selection
description: >
  Dynamic skill orchestrator. Scans every installed skill, plugin, and MCP
  server in your environment, classifies each as infrastructure (always-on)
  or project-specific, selects only the minimum necessary skills for the
  current build, and hard-suppresses everything else. Eliminates prompt
  pollution, skill conflicts, and context bloat. Platform-agnostic — works
  on Antigravity, Claude Code, Gemini CLI, Cursor, Windsurf, and any AI
  coding assistant that supports markdown-based skills.
argument-hint: "[select|add <name>|drop <name>|reset|status]"
license: MIT
---

# Skill Selection

A dynamic skill orchestrator. Scans every skill in your environment, classifies
each one, and locks a minimal manifest so the agent uses only what the current
project needs — nothing more, nothing less.

**This skill contains zero hardcoded skill names.** It works with whatever you
have installed, on any platform, for any project type.

---

## When to Trigger

Activate this skill when:

- The user types `/skill-selection` or asks to "select skills", "lock skills",
  "pick skills", "choose skills", or "which skills do we need"
- The user has finalized an implementation plan and wants focused execution
- The user wants to audit which skills are active and why

### Sub-Commands

| Command | Action |
|---------|--------|
| `/skill-selection` or `/skill-selection select` | Full workflow: discover, classify, select, lock |
| `/skill-selection add <name>` | Add a suppressed skill to the active manifest |
| `/skill-selection drop <name>` | Remove an active project skill (blocked for protected) |
| `/skill-selection reset` | Clear the manifest, restore default behavior |
| `/skill-selection status` | Display the current manifest without changes |

---

## The Workflow

### Pre-Check: Is This Even Necessary?

Before running the full workflow, evaluate the task scope:

- **Trivial tasks** (fix a typo, change a color, rename a variable, answer a
  quick question): Do NOT run skill-selection. Just do the work. Skill-selection
  exists for projects and sprints, not micro-edits.
- **Single-file changes** with no design, architecture, or methodology
  decisions: Probably not needed. Use your judgment.
- **Multi-file projects, new builds, sprints, hackathons, or any task where
  multiple skills COULD compete**: Run the full workflow.

If the user explicitly asks for `/skill-selection`, always run it regardless of
task size.

---

Execute these four phases in strict order. Do not skip or reorder phases.

---

### Phase 1: Locate the Plan

Before selecting skills, you must know what is being built.

1. Check the current conversation for an approved implementation plan — from a
   prior `/grill-me`, `/plan`, brainstorming session, or explicit description.
2. If none found, check the workspace for `PLAN.md`, `TODO.md`, `README.md`,
   or any plan artifact in the project root.
3. If no plan exists anywhere, stop and ask:

   > "I need to understand what we're building before I can select skills.
   > Describe the project briefly, or run `/grill-me` first to lock the plan."

4. Do NOT proceed until you clearly understand:
   - **What** is being built (web app, mobile app, CLI, ML pipeline, API, game, etc.)
   - **Tech stack** (languages, frameworks, databases, APIs)
   - **Context** (hackathon, production, prototype, portfolio, learning project)

---

### Phase 2: Discover All Installed Skills

Scan the environment to build a complete inventory. You are looking for three
types of items: **skills**, **plugins** (which contain skills), and **MCP servers**.

#### Platform Detection

Detect which platform you are running on by checking which of these paths and
patterns exist. Scan ALL paths that are present — many users have skills in
multiple locations.

**Antigravity / Gemini CLI:**
```
~/.gemini/config/skills/*/SKILL.md
~/.gemini/config/plugins/*/skills/*/SKILL.md
~/.gemini/antigravity/builtin/skills/*/SKILL.md
.gemini/skills/*/SKILL.md                        (workspace)
.agents/skills/*/SKILL.md                        (workspace)
```

**Claude Code:**
```
~/.claude/commands/*.md
.claude/commands/*.md                             (workspace)
CLAUDE.md                                         (root instructions)
```

**Cursor:**
```
~/.cursor/rules/*.mdc
.cursor/rules/*.mdc                               (workspace)
.cursorrules
```

**Windsurf:**
```
~/.windsurf/rules/*.md
.windsurf/rules/*.md                               (workspace)
.windsurfrules
```

**Generic / Unknown Platform:**
```
Scan for skills/, rules/, commands/ directories in:
  - User home config directories (~/.config/, ~/.)
  - Workspace root and .agents/ directory
  - Any path the platform documentation specifies
```

#### What to Extract

For each discovered skill, record:

| Field | Source |
|-------|--------|
| **Name** | Frontmatter `name:` field, or directory/file name as fallback |
| **Description** | Frontmatter `description:` field, or first non-heading paragraph |
| **Source path** | Absolute path to the skill file |
| **Parent plugin** | If the skill lives inside a plugin directory, note the plugin name |

Also scan for **MCP servers**:
- Check MCP config files (`mcp_config.json`, `.mcp.json`, `mcp/` directories)
- Record each server name and a brief description of its tools

Build the complete inventory before proceeding. If the inventory is empty,
inform the user that no skills were found and stop.

---

### Phase 3: Classify Every Skill

Classify each discovered item into exactly one category:
**INFRASTRUCTURE** or **PROJECT**.

#### INFRASTRUCTURE (Auto-Protected — Never Suppressed)

A skill is infrastructure if its primary purpose is to enhance the agent's
cognitive capabilities, memory, reasoning, or operational tooling — regardless
of what project is being built.

**Mark as INFRASTRUCTURE if the skill's name or description matches ANY of
these patterns:**

1. **Reasoning & Thinking** — Enhances logical reasoning, step-by-step analysis,
   chain-of-thought, reflection, or decision-making quality
2. **Memory & Context** — Manages conversation memory, context windows,
   knowledge persistence, session continuity, or retrieval-augmented recall
3. **Code Intelligence** — Builds or queries code graphs, dependency maps,
   symbol indexes, or codebase-wide architectural understanding
4. **Output Quality Control** — Enforces complete code generation, bans
   placeholder patterns (`// TODO`, `...`), prevents truncation, or controls
   output fidelity and completeness
5. **Agent Orchestration** — Manages how the agent dispatches work, tracks
   tasks, coordinates sub-agents, executes plans, or handles parallelism
6. **Debugging Process** — Defines systematic debugging methodology (how to
   approach bugs — not a specific test framework or assertion library)
7. **Research & Discovery** — Enhances web search, documentation lookup, live
   information gathering, or knowledge verification capabilities
8. **Platform Adapters** — Adapts agent behavior to a specific IDE or CLI
   (tool mappings, harness-specific instructions, environment detection)
9. **Meta-Skills** — Skills about managing other skills, session setup, agent
   self-configuration, or skill orchestration (including this skill itself)
10. **MCP Servers** — ALL MCP servers are infrastructure by default. They
    provide tool-level capabilities, not project-specific aesthetic or
    methodology rules.

**Core principle:** If removing this skill would make the agent _dumber_ or
_less capable_ on ANY project — not just the current one — it is infrastructure.

#### PROJECT (Eligible for Selection / Suppression)

A skill is a project skill if it defines, constrains, or directs the specific
_output_ of a build — aesthetics, architecture patterns, methodology choices,
framework conventions, or domain-specific implementation rules.

**Mark as PROJECT if the skill's name or description matches ANY of these
patterns:**

1. **Visual Design & Aesthetics** — UI styles, CSS philosophies, design systems,
   typography rules, color palettes, animation patterns, layout grids, or
   specific visual themes (brutalist, minimalist, glassmorphism, etc.)
2. **Framework-Specific Patterns** — Prescribes how to use a specific framework
   or library (React patterns, Flutter conventions, Django idioms, etc.)
3. **Testing Methodology** — Enforces a specific testing approach (TDD, BDD,
   E2E with Playwright/Cypress, specific assertion libraries)
4. **Coding Philosophy** — Defines coding style preferences (minimal code,
   enterprise patterns, clean architecture, hexagonal, etc.)
5. **Domain-Specific Content** — Skills for specific output domains (mobile
   design, banner creation, slide generation, brand identity, SEO, social
   media assets, logo design)
6. **Deployment Patterns** — Specific deployment or infra patterns (Docker
   workflows, CI/CD templates, cloud-specific conventions)
7. **Content & Copy** — Writing style, humanizing text, brand voice,
   copywriting formulas, presentation rhetoric

**Core principle:** If this skill is highly relevant to SOME projects but
irrelevant or actively harmful to others, it is a project skill.

#### Ambiguous Cases

If a skill does not clearly fit either category:

1. Default classification: **PROJECT** (safer — it can be re-added)
2. Flag it in the output with the AMBIGUOUS marker
3. Let the user make the final call

#### User Override File

If a `.skill-selection.json` file exists in the workspace root OR in the
user's global config directory, read it for manual overrides:

```json
{
  "always_protected": ["my-memory-enhancer", "company-standards"],
  "always_suppressed": ["legacy-skill-i-hate"]
}
```

These overrides take **absolute precedence** over heuristic classification.
A skill listed in `always_protected` is infrastructure, period.
A skill listed in `always_suppressed` is suppressed, period — even if it
would otherwise match the project.

---

### Phase 4: Select, Lock, and Output

Match the classified PROJECT skills against the implementation plan from
Phase 1 and produce the final manifest.

#### Selection Rules

1. **One skill per domain.** If multiple project skills cover the same domain
   (e.g., three competing UI/design skills, or two competing testing
   methodologies), select ONLY the single best match for the project's
   stated goals, stack, and context. Never activate competing skills — this
   is the #1 source of prompt pollution.

2. **Minimum necessary set.** Activate a project skill ONLY if the plan
   explicitly requires its domain. Do not speculatively activate "nice to
   have" skills. When in doubt, suppress.

3. **Justify every selection.** For each activated skill, write one sentence
   explaining WHY it was selected for THIS specific project — not a generic
   description of the skill.

4. **Explain contested decisions.** When choosing between competing skills in
   the same domain, briefly explain why the selected one won and why the
   others were suppressed.

#### Output the Manifest

Display the manifest in this format:

```
╔═══════════════════════════════════════════════════════════╗
║            SKILL MANIFEST — [Project Name]                ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  [PROTECTED] Infrastructure — always active:              ║
║    • [name] — [why it's infrastructure]                   ║
║    • [MCP: name] — [brief capability]                     ║
║                                                           ║
║  [ACTIVE] Selected for this build:                        ║
║    • [name] — [why selected for THIS project]             ║
║                                                           ║
║  [SUPPRESSED] Silenced for this session:                  ║
║    • [name] — [why not needed / which active covers it]   ║
║                                                           ║
║  [AMBIGUOUS] Override with /skill-selection add:          ║
║    • [name] — [why classification is unclear]             ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║  Total: X protected | Y active | Z suppressed            ║
╚═══════════════════════════════════════════════════════════╝
```

After displaying, ask the user:

> "Does this selection look right? Say **go** to lock it, or tell me what
> to add/drop."

Do NOT issue the suppression lock until the user confirms.

#### Issue the Lock (After User Confirmation)

Once the user approves, issue this directive:

> **SKILL MANIFEST LOCKED.**
>
> For the remainder of this session:
>
> 1. **ACTIVE PROJECT SKILLS** — read, invoke, and follow these when relevant.
> 2. **PROTECTED INFRASTRUCTURE** — always active. Use normally.
> 3. **SUPPRESSED SKILLS** — do NOT read, invoke, reference, quote, or apply
>    any rules, patterns, styles, templates, or instructions from these skills.
>    Treat them as if they are not installed. If a user request would normally
>    trigger a suppressed skill, handle it using only active skills and base
>    capabilities.
> 4. **No phantom rules** — do not apply design patterns, coding conventions,
>    or aesthetic rules from skills outside the active manifest, even if you
>    recall them from prior conversations or training data.
>
> This lock remains active until `/skill-selection reset`, an explicit user
> override, or a new conversation.

#### Persist to Workspace (If Possible)

If the platform supports file creation, save the manifest to the workspace
root as `.skill-manifest.json`:

```json
{
  "skill_selection_version": "1.0",
  "project": "[project name]",
  "created_at": "[ISO 8601 timestamp]",
  "platform": "[detected platform name]",
  "plan_summary": "[one-line summary of what is being built]",
  "protected": [
    { "name": "...", "type": "skill|mcp", "reason": "..." }
  ],
  "active": [
    { "name": "...", "reason": "..." }
  ],
  "suppressed": [
    { "name": "...", "reason": "..." }
  ],
  "ambiguous": [
    { "name": "...", "reason": "..." }
  ]
}
```

On subsequent conversations in the same workspace, if `.skill-manifest.json`
exists, read it and auto-apply the lock. Inform the user:

> "Found existing skill manifest for **[project]**. Auto-applying.
> Run `/skill-selection status` to review or `/skill-selection reset` to clear."

---

## Mid-Session Commands

### `/skill-selection add <name>`

1. Find the skill in the suppressed or ambiguous list.
2. If not found there, check if it exists in the environment at all.
3. Move it to the active list.
4. Briefly state what it will contribute to the current project.
5. Update `.skill-manifest.json` if it exists.
6. The suppression of all other suppressed skills remains in full effect.

### `/skill-selection drop <name>`

1. If the skill is in the **protected** list, REFUSE:
   > "[name] is infrastructure — it enhances [capability] on every project.
   > To force-suppress it, add it to `always_suppressed` in
   > `.skill-selection.json`."
2. If the skill is in the **active** list, move it to suppressed.
3. Update `.skill-manifest.json` if it exists.

### `/skill-selection reset`

1. Clear the suppression directive. All skills return to default behavior.
2. Delete `.skill-manifest.json` if it exists.
3. Inform the user that all installed skills are now available again.
4. Suggest re-running `/skill-selection` if they want to re-lock.

### `/skill-selection status`

1. Display the current manifest in the standard output format.
2. If no manifest is active, say so and suggest running `/skill-selection`.

---

## Safety Rules

These are non-negotiable. They protect the user's environment.

1. **Never suppress infrastructure.** The protected layer cannot be bypassed
   through the interactive flow. The ONLY way to suppress an infrastructure
   skill is the `always_suppressed` override in `.skill-selection.json` — a
   deliberate, manual, file-level decision by the user.

2. **Never suppress this skill.** `skill-selection` is always protected.
   It cannot suppress, drop, or lock itself out.

3. **Transparency over silence.** If classification is uncertain, flag it
   as ambiguous. Never silently suppress a skill the user might depend on.

4. **User has final say.** If the user disagrees with any classification
   after seeing the manifest, honor their override immediately via `add` or
   `drop`. Do not argue or re-classify against explicit user intent.

5. **No hallucinated skills.** Only include skills that were actually
   discovered in Phase 2. Never invent, assume, or "remember" skills that
   were not found in the scan.

6. **Re-scan on reset.** When the user runs `reset` then a new `select`,
   re-scan from scratch. Skills may have been installed or removed since
   the last scan.

7. **Confirm before locking.** Always show the manifest and wait for user
   approval before issuing the suppression directive. Never auto-lock
   without confirmation on the first run.

---

## Platform Installation

This skill is a single folder. Copy it to your platform's skill directory:

| Platform | Install Path |
|----------|-------------|
| **Antigravity** | `~/.gemini/config/skills/skill-selection/` |
| **Gemini CLI** | `~/.gemini/config/skills/skill-selection/` |
| **Claude Code** | Copy to `.claude/commands/skill-selection.md` or include in `CLAUDE.md` |
| **Cursor** | Copy to `.cursor/rules/skill-selection.mdc` |
| **Windsurf** | Copy to `.windsurf/rules/skill-selection.md` |
| **Other** | Place in whatever directory your platform scans for skills/rules |

---

## FAQ

**Q: Does this actually unload skills from memory?**
A: It depends on the platform.

- **Antigravity / Gemini CLI (progressive disclosure):** Highly effective.
  Skills only load their full instructions when the agent decides to read them.
  If the suppression directive says "don't read skill X," the agent never
  triggers the activation step — the skill's content never enters the context
  window. This is close to true system-level suppression.
- **Claude Code / Cursor / Windsurf (pre-loaded rules):** Effective but softer.
  If a platform has already loaded rule content into the context window before
  this skill runs, the suppression directive tells the agent to ignore that
  content. The tokens are still consumed, but the agent stops acting on them.
  For best results on these platforms, ensure skill-selection runs early.

**Q: Does this actually save tokens / reduce cost?**
A: Yes, on platforms with progressive disclosure. Every skill the agent reads
adds hundreds to thousands of tokens to the context. Suppressing 20 skills
that the agent would otherwise read can save 10,000–40,000 tokens per session.
That translates directly to faster responses and lower API costs. On platforms
that pre-load everything, the token savings are minimal, but the quality
improvement (no conflicting instructions) is still significant.

**Q: What if I install a new skill mid-session?**
A: Run `/skill-selection status` to see the current lock, then
`/skill-selection add <name>` to include it, or `/skill-selection reset`
followed by a fresh `/skill-selection` to re-scan everything.

**Q: Can I share manifests with my team?**
A: Yes. Commit `.skill-manifest.json` and/or `.skill-selection.json` to your
repo. Teammates with the same skills installed will get the same lock applied
automatically.

**Q: What if a suppressed skill has an `EXTREMELY-IMPORTANT` tag?**
A: Some aggressively-written skills use high-priority directives in their own
SKILL.md to force activation. On progressive-disclosure platforms, this is not
a problem because the skill's content is never loaded. On pre-loaded platforms,
a sufficiently aggressive skill could theoretically override the suppression.
If you encounter this, use the platform's native disable mechanism (uninstall
or move the skill file) rather than relying on prompt-level suppression alone.
