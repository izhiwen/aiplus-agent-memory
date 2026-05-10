# AiPlus Agent Memory

[中文 README](README.zh-CN.md)

## The Problem

Monday morning, you spend twenty minutes teaching the agent your project's naming conventions. By Wednesday, the agent has forgotten whether to use camelCase or snake_case. You add a memory file to help, but then worry it might contain a private preference or an API key snippet you accidentally pasted last week. You want the agent to remember, but you cannot risk leaking secrets into a public repository.

## What It Does

AiPlus Agent Memory stores project conventions as JSONL records under `.aiplus/memory/`. Each record contains a memory entry, a role identity, or a skill candidate proposal. Before any record is written, twelve automated redaction patterns strip sensitive strings:

- Passwords and API keys
- JWT tokens and auth headers
- Raw transcripts and provider request/response bodies
- Private paths and personal identifiers

A `memory doctor` command runs automated health checks: stale records, conflicts between entries, schema violations, and orphan files. Rejected or forgotten records remain in the store for audit purposes but are hidden from the agent's context by default.

Role identities define how the agent should behave: Advisor for strategy, CEO for planning, Reviewer for critique, Builder for implementation. Switch roles with natural language commands.

## Installation

With AiPlus already installed, memory is enabled automatically:

```bash
cd MyProject
aiplus install codex        # or: claude-code, opencode, all
aiplus memory status
```

Or use the standalone schemas and templates:

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

Each runtime gets project-local adapter files that enable natural language memory commands in the agent session.

## How It Works

Natural language commands in your agent session map to memory operations:

```text
Remember we use camelCase for functions    → aiplus memory add
What do you remember about this project?   → aiplus memory status
Switch to advisor mode                      → aiplus identity context --role advisor
Forget that rule about tabs                 → aiplus memory forget <id>
```

## Repository Structure

- `core/schemas/` — JSON schemas for memory-record, identity, skill-candidate, audit-event, and role-identity
- `core/templates/` — Memory record templates and role identity definitions (advisor, ceo, reviewer, builder)
- `core/docs/` — Memory model, protocol, redaction policy, role identity, skill candidate lifecycle, safety boundaries
- `adapters/codex/` — Codex skills for memory and identity commands
- `adapters/claude-code/` — Claude Code memory integration and commands
- `adapters/opencode/` — OpenCode memory prompts and agents
- `examples/` — Safe and blocked memory record examples showing redaction in action
- `tests/` — Test fixtures and validation cases

## Commands

```bash
# Memory management
aiplus memory init --project              # Initialize project memory store
aiplus memory status                      # Show record counts and health
aiplus memory doctor                      # Run automated health checks
aiplus memory context --runtime codex     # Build context packet for agent
aiplus memory add --scope project         # Add a project-scoped memory
aiplus memory search "naming"             # Search memory records
aiplus memory forget <id>                 # Remove a record from context

# Identity management
aiplus identity init --project            # Initialize role identities
aiplus identity context --role advisor    # Load advisor context
aiplus identity context --role ceo        # Load CEO context

# Skill candidates
aiplus skill-candidate status             # Show proposed skills
```

## Safety

AiPlus Agent Memory does not:
- Upload memory data or transcripts to any service
- Implement cloud sync or vector databases
- Automatically learn from transcripts without explicit approval
- Automatically approve skills (candidates require explicit acceptance)
- Store secrets in memory records (redaction blocks them before writing)
- Share data between projects (each project has isolated memory)

## More Information

See the [main AiPlus repository](https://github.com/izhiwen/aiplus) for the complete platform.

Current gaps and planned work: [v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md).

## License

[Apache-2.0](LICENSE)
