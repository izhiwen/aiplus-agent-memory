# AiPlus Agent Memory

[中文 README](README.zh-CN.md)

## The Pain

You teach the agent your project's naming conventions on Monday. By Wednesday it has forgotten. You add a memory file, but then worry it might contain a private preference or an API key snippet you accidentally pasted. You want the agent to remember, but you do not want to leak secrets into a public repository.

## The Solution

AiPlus Agent Memory stores project-local records as JSONL under `.aiplus/memory/`. Before any record is written, twelve redaction patterns automatically strip sensitive strings like passwords, JWTs, and raw transcripts. A `memory doctor` self-checks for stale records, conflicts, and schema violations. Rejected or forgotten records stay in the store but are hidden from context by default. Everything is local. Nothing uploads. Nothing leaves your machine.

## Quick Start

If you already have AiPlus, memory is enabled automatically when you install any module:

```bash
cd MyProject
aiplus install codex        # or: claude-code, opencode, all
aiplus memory status
```

Or clone the standalone schemas and templates:

```bash
git clone https://github.com/izhiwen/aiplus-agent-memory.git
cd aiplus-agent-memory
```

## Runtime Support

Memory works with all three supported agents. Install AiPlus for your runtime:

```bash
aiplus install codex        # Codex
aiplus install claude-code  # Claude Code
aiplus install opencode     # OpenCode
```

Each runtime gets project-local adapter files that enable natural language memory commands.

## What's Inside

- `core/schemas/` — JSON schemas for memory-record, identity, skill-candidate, audit-event
- `core/templates/` — Memory record and identity templates
- `core/docs/` — Memory model, protocol, redaction policy, role identity docs
- `adapters/codex/` — Codex skills for memory commands
- `adapters/claude-code/` — Claude Code memory integration
- `adapters/opencode/` — OpenCode memory prompts
- `examples/` — Safe and blocked memory record examples

## Common Commands

```bash
aiplus memory init --project              # Initialize project memory
aiplus memory status                      # Show memory records count
aiplus memory doctor                      # Run memory health checks
aiplus memory context --runtime codex     # Build context packet
aiplus memory add --scope project         # Add a project memory
aiplus memory search "naming"             # Search memories
aiplus memory forget <id>                 # Forget a memory
aiplus identity init --project            # Initialize role identities
aiplus identity context --role advisor    # Load advisor context
```

## Safety Boundaries

AiPlus Agent Memory does not:
- Upload memory data or transcripts
- Implement cloud sync or vector databases
- Automatically learn from transcripts
- Automatically approve skills
- Store secrets in memory records (redaction blocks them)

## Roadmap

See the [main AiPlus repo](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md) for current gaps.

## License

[Apache-2.0](LICENSE)
