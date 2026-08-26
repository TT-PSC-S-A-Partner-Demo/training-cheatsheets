# Cost & Security - the one-pager

*AI-assisted SDLC · Session 1 · TTPSC*

Send this after the session. Everything from the two hours on one page.

---

### 1. The loop, in one breath
- Every turn the model gets: your question + files it read + the whole chat so far.
- Then it either **answers**, or **runs something** (opens a file, runs a command).
- **Cost** lives in what goes in. **Risk** lives in what it runs.

### 2. Four cost ideas
- **Right model + effort** - cheap model + low effort for typing; strong model + high effort for real thinking. `/model`
- **Clean context** - one task per chat. `/new`, `/compact`
- **Fast mode** - only when you're waiting. `/fast`
- **Reuse the start** - keep AGENTS.md stable, don't edit it mid-task.

**Traps that cost the most:** one endless chat (re-billed every turn); editing AGENTS.md or switching model/tool mid-task (breaks the cache, full price again); high effort on trivial work; "improve the whole project" (reads the entire repo). Each shows up as a bigger number on `/status`.

### 3. Read the meter
- Codex CLI: `/status`
- Claude Code: `/cost`
- Any other tool: find its usage readout, check it after a task.
- **Habit:** measure before and after any change. No number, no argument.

### 4. What you pay for (and not all tokens cost the same)
- **Input** - 1x baseline. Prompt, files, whole chat. Compounds every turn.
- **Output** - ~5x input. Reply plus reasoning. The pricey half - shorter answers save real money.
- **Cache write** - ~1.25x input. First time a stable prefix is stored.
- **Cache read** - ~0.1x input. Every reuse of that prefix after. Keep the prefix stable = near-free repeats.
- Ratios are the point; exact prices move, read your model's card.

---

### 5. Sandbox & approvals
- **read-only** - meet any new code here.
- **workspace-write** - sane daily default.
- **full access** - the name is a warning.
- Approvals **on-request**; network off in the sandbox on purpose.

### 6. Can this go in a prompt? (local model / approved vendor tool / never)
- **Approved vendor tool - fine:** your own code, a plain stack trace.
- **Redact first, then a vendor tool:** anything with a customer email, account, PII.
- **Never (local only if the NDA allows):** live keys, secrets, a client's NDA material.
- Sensitivity is in the payload, not the file type.

### 7. Cost tools, if they fit
*Advertised numbers are theirs, not yours. Each trades something away - measure the quality, not just the tokens. Verify each command on the repo - installers drift.*
- **caveman** - terse-output framework (lite/full/ultra), cuts output tokens. *Watch: loses nuance - off for careful prose.* [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman)
  - `claude plugin marketplace add JuliusBrussee/caveman && claude plugin install caveman@caveman` (also Codex, Cursor, ...)
- **RTK** - CLI proxy, trims noisy command output (git/build/test). *Watch: can hide a line you wanted (an error, a warning).* [rtk-ai/rtk](https://github.com/rtk-ai/rtk)
  - `cargo install --git https://github.com/rtk-ai/rtk`, then `rtk init -g` (name clash: not "Rust Type Kit")
- **Ponytail** - pushes the agent to write less code. *Watch: can under-build - skipped edge cases.* [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail)
  - Codex: `codex plugin marketplace add ...` · Claude Code: `/plugin marketplace add` + `/plugin install`
- **Headroom** - compresses tool output before the model, **and doubles as a local token/cost monitor** (proxy + dashboard, data stays on your machine). *Watch: can drop detail the model needed.* [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom)
  - `uv tool install --python 3.13 "headroom-ai[all]"` (or `pip install headroom-ai`) · macOS GUI: [headroom-desktop](https://github.com/gglucass/headroom-desktop)

### 8. Slash commands most people miss
*Know your tool - these are not interchangeable.*

**Claude Code**
- `/context` - visual grid of what's eating your context window, and what frees it.
- `/resume` - jump back to an earlier conversation.
- `/cost` (alias `/usage`) - tokens and spend so far.
- `/export` - dump the conversation to a file or clipboard.
- `/memory` - edit CLAUDE.md and view auto-memory.
- `/agents` - manage subagents.
- `/hooks` - view your hook configuration.

**Codex**
- `/approvals` , `/permissions` - set what runs without asking you each time.
- `/undo` - revert the last change it made this session.
- `/review` - review uncommitted, a commit, or vs a base branch.
- `codex exec "..."` - non-interactive; scriptable / CI.
- `codex resume` / `codex --continue` - reopen a recent chat instead of starting cold.
- `codex cloud` - offload to a cloud env, results come back local.
- `@path/to/file` focus context · `--image x.png` screenshot in · `--search` live web.
- `codex --sandbox ...` safer run · `codex --full-auto` no prompts (know the risk).

**Devin**
- `devin -p "prompt"` - single-turn: print the answer and exit (scriptable).
- `devin -c` / `devin -r` - continue the last / resume a past session.
- `/loop [prompt]` - run a prompt, then auto-review the diff in a loop.
- `/ask [question]` - answer without changing code.
- `/plan` / `/accept-edits` - switch modes; Plan looks before it edits.
- `/ls --all` - list sessions across directories.

---

*One task, one chat · measure before and after · check the account before you paste · TTPSC*
