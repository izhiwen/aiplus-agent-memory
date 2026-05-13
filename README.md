# AiPlus Agent Memory
[![CI](https://github.com/izhiwen/AiPlus-Agent-Memory/actions/workflows/ci.yml/badge.svg)](https://github.com/izhiwen/AiPlus-Agent-Memory/actions/workflows/ci.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

[中文 README](README.zh-CN.md)

## The pain

Your agent has total amnesia between sessions. Monday you teach it the
project's naming conventions. Wednesday it asks again whether you prefer
camelCase or snake_case. By Friday you have explained the same architectural
decision four times to four fresh sessions.

So you think: "fine, just write it into memory." And immediately you stop —
because what if a record leaks a private preference you mentioned in passing?
What if last week's API key is still sitting in a transcript that ends up in
the store? You want the agent to remember, but you cannot risk a secret
surfacing in a shared repository.

## What we do about it

AiPlus Agent Memory is a project-local JSONL memory store that gives the
agent a real working memory **without** giving up control over what gets
remembered.

- **Records live under `.aiplus/memory/` inside your project.** Each project
  has its own isolated memory. Nothing crosses projects, nothing leaves the
  machine.
- **Twelve redaction patterns run before any record is written.** Passwords,
  API keys, JWT tokens, authorization headers, raw transcripts, provider
  request/response bodies, private file paths, personal identifiers — all
  stripped at the source. A record that would contain a secret is rejected,
  not "redacted later".
- **`memory doctor` self-checks the store.** Stale records, conflicting
  entries, schema violations, orphan files — all surfaced before they cause
  silent confusion.
- **Rejected and forgotten records stay in the store for audit, but stay
  hidden from the agent's context by default.** You keep the history without
  re-injecting the bad data.

No network call. No cloud sync. No vector database. No upload. Ever.

## Quick start

Memory ships with the AiPlus bundle. If you already have AiPlus installed:

```bash
cd MyProject
aiplus add agent-memory       # add module (no-op if already bundled)
aiplus install codex          # or: claude-code, opencode, all
aiplus memory status
```

`aiplus memory status` tells you whether the store is initialized, how many
records are active, and whether `memory doctor` found any issues.

## Repository structure

- `core/schemas/` — JSON schemas for memory records, identities, skill
  candidates, audit events, role identities, memory reviews
- `core/templates/` — record templates and role identity definitions
  (`advisor`, `ceo`, `reviewer`, `builder`)
- `core/docs/` — memory model, protocol, redaction policy, role identity
  reference, skill candidate lifecycle, safety boundaries, QA checklist
- `adapters/codex/` — Codex skills for memory and identity commands
- `adapters/claude-code/` — Claude Code memory integration
- `adapters/opencode/` — OpenCode memory prompts and agent configs
- `examples/` — safe and blocked record examples, accepted and rejected
  skill candidates
- `tests/fixtures/` — validation fixtures

## Safety boundaries

AiPlus Agent Memory does not:

- upload memory data or transcripts to any service
- implement cloud sync or a vector database
- learn from transcripts automatically (an explicit accept step is required)
- approve skill candidates automatically (each requires explicit acceptance)
- store secrets in records (redaction blocks them before writing)
- share data between projects (each project's memory is isolated)

## Contributing

We welcome contributions that stay within the plugin's scope (local
project-scoped agent memory with strict redaction, not a generic vector
database or cloud-synced memory).

1. **Open an issue first** for anything larger than a typo fix.
2. **Add a redaction fixture** for every new redaction pattern under
   `tests/fixtures/` so the rule is covered before the code lands.
3. **Adapter parity** — if you change CLI surface, update all three
   adapters (`adapters/codex/`, `adapters/claude-code/`, `adapters/opencode/`).
4. **Run `aiplus memory doctor`** after schema changes to validate the store.

## More

- Main platform: [AiPlus](https://github.com/izhiwen/AiPlus)
- Tracked work before next release:
  [v0.5.2 known gaps](https://github.com/izhiwen/AiPlus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
