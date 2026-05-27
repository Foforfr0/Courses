# Folder structure

Source: [**Instagram** - _Claude folder structure - leadgenman_](https://www.instagram.com/p/DXGw_kcDeaS/?img_index=1)

|-- 📁 Project \
|-- 📝 [CLAUDE.md](#CLAUDEmd) \
|-- 📝 [CLAUDE.local.md](#CLAUDElocalmd) \
|-- 📝 [.mpc.json](#mcpjson----your-MCP-server-roster) \
|-- 📁 .claude \
|-- |-- 📝 [settings.json](#settingsjson--localjson---config-layers) \
|-- |-- 📝 settings.local.json \
|-- |-- 📁 [agents](#claudeagents----subagents) \
|-- |-- |-- 📝 code-reviewer.md \
|-- |-- |-- 📝 debugger.md \
|-- |-- |-- 📝 test-writter.md \
|-- |-- |-- 📝 refactorer.md \
|-- |-- |-- 📝 doc-writer.md \
|-- |-- |-- 📝 security-auditor.md \
|-- |-- 📁 [hooks](#claudehooks----deterministic-automation) \
|-- |-- |-- 📝 pre-commit.sh \
|-- |-- |-- 📝 lint-on-save.sh \
|-- |-- |-- 📝 session-start.sh \
|-- |-- 📁 [rules](#clauderules----path-scoped-instructions) \
|-- |-- |-- 📝 api.md \
|-- |-- |-- 📝 database.md \
|-- |-- |-- 📝 frontend.md \
|-- |-- 📁 [skills](#claudeskills----replaces-commands/) \
|-- |-- |-- 📁 commit-push-pr \
|-- |-- |-- |-- 📝 SKILL.md \
|-- |-- |-- 📁 review-pr \
|-- |-- |-- |-- 📝 SKILL.md \

## Composition

### CLAUDE.md:

Living doc. Boris updates his multiple times a week. Every mistake becomes a new lines so it never repeats.

Consists on:

- Project´s operating manual.
- Loads every session.
- Repo's root.
- Target: under 200 lines.
- Not the system prompt, it's a user message.

For example:

- Development standarts:
  - Philosophy:
    - No speculative features.
    - Justify new dependencies.
    - Remove dead code. Finis the job.
  - Hard limits:
    - Functions <= 100 lines.
    - Complexity <= 8. 100.char lines.
  - Tool chain -- always bun:
    - bun run typecheck
    - bun run test -- -t "name"
    - bun run lint && bun run test
  - Git
    - Features branches + PRs.
    - Commits: imperative, <= 72 chars.
    - Use trash, never rm -rf.

### CLAUDE.local.md

This never enters the shared repo. Keep personal paths, DB names, and "when I say X" shortcuts here -- out of the commited CLAUDE.md.

Consists on:

- Gitignored.
- Machine-local.
- Never shared.
- Loads alongside CLAUDE.md every session.
- Auto-added to .gitignore by /init.
- Personal paths, secret pointers, shortcuts.

For example:

- Personal overrides (gitignored)
  - Environment
    - Supabase project: tiltit-dev-xyz
    - Scratch dir: /Users/manthan/tmp
    - Stripe test key in 1Password
  - Shortcuts:
    - "ship it fast" -> skip test.
    - "deep dive" -> 3 parallel agents.
    - "clean up" -> delete tmp/.
  - Local workflow
    - Dev server :8000 (not :3000).
    - iTerm2 splits, not tabs.
  - Current focus
    - CLAUDE.md starter pack carousel.
    - NM-XXX commit format.

### .mcp.json -- your MCP server roster

Secrets never go in .mcp.json --${ENV_VAR} expands from your shell at launch, safe to commit.

Consist on:

- Project-scoped MCP server config.
- MUST live at project root. NOT in .claude/
- Live tools: browsers, DBs, APIs, files.
- Commited = whole team gets the tools.

For example:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    },
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server"],
      "env": {
        "SUPABASE_ACCESS_TOKEN": "${SUPABASE_TOKEN}"
      }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "server-github"]
    }
  }
}
```

### .claude/agents/ -- subagents

Runs in its own context, so a 3000-line diff never leaks into your main session -- you only see the critical/warnings report.

Consists on:

- Task-specific subagents.
- Own context, own tools, own permissions.
- Return only a summary to a main context.
- Use when side task pollutes main context.

For example:

\--- \
name: code-reviewer. \
description: Expert reviewer. \
tools: Read, Grep, Glob, Bash. \
model: sonnet \
\---

Senior reviewer. Run git diff \
-- staged and review every file.

1. Security
   - Hardcoded secrets, API keys
   - SQL injection, XSS, SSRF
   - Missing auth on endpoints
2. Correctness
   - Null/undefined, off-by-one
   - Race conditions, missing await
3. Performance
   - N+1 queries, 0(n^2) loops

End with: SHIP / FIX / BLOCK.+

### .claude/hooks/ -- deterministic automation

CLAUDE.md suggest. Hooks run every time -- guaranteed.

Consists on:

- Hooks fire on lifecycle events.
- PreToolUse, PostToolUse, Stop, SessionStart.
- Unlike CLAUDE.md, hooks ALWAYS run.
- For anything that MUST happen automatically.

For example:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "grep -q 'rm -rf /' && exit 2"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bun run lint --silent"
          }
        ]
      }
    ]
  }
}
```

### .claude/rules/ -- path-scoped instructions

Only enters context when Claude reads a file under app/api/\*\*. Main CLAUDE.md stays slim and API rules never pollute frontend edits.

Consists on:

- Modular CLAUDE.md fragments in rules/\*.md.
- YAML frontmatter with path: glob.
- Loads ONLY when matching files are touched.
- Keeps main CLAUDE.md lean -- zero startup cost.

For example:

\--- \
paths: \
 \- "app/api/\*\*/\*.ts" \
 \- "src/api/\*\*/\*.{ts,tsx}" \
\--- \

- API Route Rules
  - Validation
    - Parse every body with Zod.
    - Schemas in lib/schemas/.
    - Reject 400 on safeParse fail.
  - Auth
    - requireAuth() at top of handler.
    - 401 {error: "unauthorized"}.
      UUID-validate before DB write.
  - Rate limiting
    - Public: withRateLimit(10/1m).
    - Checkout/webhook: max 3.
  - Logging
    - Redact password, token, email.

### .claude/skills/ -- replaces commands/

Skills replaces .claude/commands in late 2025 -- same slash invocation, directory per skill so you can bundle script + templates.

Consists on:

- 2025 canonical home for slash commands.
- skills/\<name>/SKILL.md + supporting files.
- Model - invokable or user-invokable.
- Open standard (agentskills.io).

For example:

\--- \
name: commit-push-pr \
description: Stage, commit push, PR. \
argument-hint: [pr-title] \
allowed-tools: Bash(git \*) Bash(gh \*) \
\--- \

\- commit-push-pr

1. Pre-flight \
   \- git status --short (abort if clean) \
   \- bun run lint && typecheck \
   \- bun test --bail
2. Stage + commit \
   \- Check no .env*, *.pem in diff. \
   \- Conventional Commit, <= 60 chars. \
   \- Never --amend or --no-verify
3. Push + PR \
   \- git push -u origin HEAD \
   \- gh pr create --base main \
   \- gh pr checks --watch

### settings.json + .local.json --config layers

Deny always wins -- rm -rf and .env reads stay blocked.

Consists on:

- settings.json: shared project (commited).
- settigns.local.json: personal (gitignored).
- Precedence: managed > local > project > user.
- Permissions, model, hooks, env live here.

For example:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "claude-opus-4-6",
  "includeCoAuthoredBy": true,
  "permissions": {
    "defaultMode": "acceptEdits",
    "allow": [
      "Read",
      "Edit",
      "Write",
      "Glob",
      "Grep",
      "Bash(bun:* )",
      "Bash(git status)",
      "Bash(git commit:* )",
      "Bash(gh pr:* )"
    ],
    "ask": ["Bash(git push:* )"],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force:* )",
      "Read(./.env)",
      "Read(./secrets/**)"
    ]
  }
}
```
