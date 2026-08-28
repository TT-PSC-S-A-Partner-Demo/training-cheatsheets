# Session 2 cheat sheet - configure the agent, then chain it

*AI-assisted SDLC - Session 2 - TTPSC. Keep this one after the session.*

Prices and commands move - verify against your tool's own docs when in doubt.

---

## 1. Which tool for which job

| You want to... | Reach for | Because |
|---|---|---|
| Say it once, this task only | a prompt | one-off, nothing to save |
| Say it every session, this project | AGENTS.md | read on every turn |
| Repeat a multi-step procedure | a skill | loads only when it matches |
| Reach a system (Jira, DB, files) | MCP | the agent acts, you stop pasting |

Common mistake: a *procedure* pushed into AGENTS.md - it burns tokens every turn. Steps -> skill. Always-true facts -> AGENTS.md.

## 2. AGENTS.md

- Read on **every turn**, so every line is taxed every time. Test: would you retype it in *every* chat? If not, cut it.
- **Under ~50 lines.** Show, don't tell (rule + a one-line example). Number priorities when rules can conflict.
- **Inline** the always-true one-liners; **link** the heavy/occasional detail (`When touching payments, read docs/payments.md`).
- Files **stack**: `~/.codex/AGENTS.md` (global) -> `<repo>/AGENTS.md` -> `<repo>/folder/AGENTS.md`. **Closest wins.**
- `/init` writes a draft - **cut it in half**. An LLM-written AGENTS.md shipped untouched is the most common way to make things worse.

## 3. Skills

- A folder + a `SKILL.md`. Frontmatter: `name` + `description`. Body = plain markdown steps.
- Lives in `~/.codex/skills/<name>/` (Codex personal) or `.codex/skills/<name>/` (Codex repo). **Devin:** `.devin/skills/<name>/` (repo) or `~/.config/devin/skills/` (global). **Same SKILL.md** - only the folder changes.
- **The description is the product** - it's always in context; the body loads only when it fires.
  - Good: "Reviews a Go diff for error wrapping and table tests. Use when asked to review a PR or check staged changes."
  - Bad: "Helps with code quality." (fires on nothing)
- Rules: name the **trigger words** a person types; be specific about what to flag/ignore; **one skill, one job**.
- **Test the trigger:** 3 real requests should fire it without you naming it; if not, fix the *description* first. Write the checks down in an `evals.json` next to the skill.
- Body under **~500 lines / 5000 tokens**; push detail to `reference/` one level deep.
- Portable: the same SKILL.md is read by Codex, Claude Code, Cursor, Gemini CLI. Install existing: `npx openskills install <owner/repo>`.

## 4. MCP

- Connect: `codex mcp add <name> -- <command>` · `codex mcp list` · `/mcp` (what this session can reach). *(Workshop kit needs `pip install "mcp[cli]<2"` - the server uses FastMCP from SDK 1.x.)*
- **Scope it** in `.codex/config.toml`:
  ```toml
  [mcp_servers.jira]
  default_tools_approval_mode = "writes"   # reads flow, writes stop and ask
  enabled_tools  = ["search_issues", "get_issue"]
  disabled_tools = ["delete_issue"]
  ```
- Start **read-only**; add writes only when a workflow needs them. A ticket body is **untrusted input**.
- **MCP vs CLI:** a standing socket loads tool schemas every turn; a CLI is called on demand. For read-a-few-things, a CLI is often the leaner front-end. Match the front-end to how often/deeply the agent needs the tool.

## 5. Orchestrator + skill chain

- One root agent drives specialised subagents; skills run as a chain (output -> input). Keep it **3-4 stages**.
- Custom subagent - `.codex/agents/reviewer.toml`:
  ```toml
  name = "reviewer"
  description = "Reviews the diff, read-only."
  sandbox_mode = "read-only"
  ```
- Register in `.codex/config.toml`:
  ```toml
  [agents]
  max_threads = 6
  max_depth   = 1          # children yes, grandchildren no
  [agents.reviewer]
  config_file = "./agents/reviewer.toml"
  ```
- Drive from one prompt: *"spec it, implement it, test it, then the reviewer checks the diff - stop between stages."*
- **Devin:** the same shape is "Devin manages Devins" - one Devin delegates to a team of managed Devins working in parallel.

## 6. Handy commands

| Codex | Devin | what |
|---|---|---|
| `/init` | (Devin plans on its own) | scaffold / plan the work |
| `/skills` | plugin / skills settings | list loaded skills |
| `$<name>` | `$<name>` | call a skill explicitly |
| `codex mcp add / list` · `/mcp` | MCP marketplace / settings | connect MCP, see reach |
| `/model` | (depth via the plan) | reasoning effort |
| `/status` | `/status` | tokens + what's loaded |
| `/new` · `/compact` | `/new` | fresh context · shrink it |
| `codex exec "..."` | `devin -p "..."` | run one non-interactive task |

**Reasoning effort** (`minimal · low · medium · high · xhigh`) is a Codex dial. Devin tunes depth through its plan, not a step setting.

**AGENTS.md** is the open standard - the repo-root file is read by **both Codex and Devin**, so one file serves both.
