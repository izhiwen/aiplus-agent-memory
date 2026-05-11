# AiPlus Agent Memory

[简体中文](README.zh-CN.md)

## The Problem

Your agent has total amnesia between sessions. You teach it your naming
conventions on Monday; by Wednesday it has forgotten whether you prefer
camelCase or snake_case. You end up repeating the same preferences three
times a week.

Adding things to memory feels risky. What if a record leaks a private
preference you mentioned in passing? What if last week's API key snippet
is still sitting in a transcript that gets stored? You want the agent to
remember, but you cannot risk secrets surfacing in a shared repository.

## The Solution

AiPlus Agent Memory stores project conventions as JSONL records under
`.aiplus/memory/`. Each record is local to the project. Nothing leaves
the machine.

Before any record is written, twelve automated redaction patterns strip
sensitive strings from the content:

- Passwords and API keys
- JWT tokens and authorization headers
- Raw transcripts and provider request/response bodies
- Private file paths and personal identifiers

A `memory doctor` command runs self-checks against the store: stale
records, conflicting entries, schema violations, and orphan files.
Rejected or forgotten records stay in the store for audit purposes but
remain hidden from the agent's context by default.

There is no network call, no cloud sync, no vector database, and no
upload.

## Quick Start

AiPlus installs memory automatically when you set up a project agent:

```bash
cd MyProject
aiplus install opencode     # or: codex, claude-code
aiplus memory status
```

The `aiplus memory status` output tells you whether the store is
initialized, how many records are active, and whether `memory doctor`
found any issues.

## What's Inside

Key directories and files:

- `core/schemas/` — JSON schemas for memory records, identities,
  skill candidates, audit events, role identities, and memory reviews
- `core/templates/` — Memory record templates and role identity
  definitions (`advisor`, `ceo`, `reviewer`, `builder`)
- `core/docs/` — Memory model, protocol, redaction policy, role
  identity reference, skill candidate lifecycle, safety boundaries, and
  QA checklist
- `adapters/codex/` — Codex skills for memory and identity commands
- `adapters/claude-code/` — Claude Code memory integration
- `adapters/opencode/` — OpenCode memory prompts and agent
  configurations
- `examples/` — Safe and blocked memory record examples, plus rejected
  and accepted skill candidates
- `tests/fixtures/` — Validation fixtures

## Safety Boundaries

AiPlus Agent Memory does not:

- Upload memory data or transcripts to any service
- Implement cloud sync or vector databases
- Automatically learn from transcripts without explicit approval
- Automatically approve skills (candidates require explicit acceptance)
- Store secrets in memory records (redaction blocks them before writing)
- Share data between projects (each project has isolated memory)

## More Info

- Main platform: [aiplus](https://github.com/izhiwen/aiplus)
- Known gaps and planned work:
  [v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
