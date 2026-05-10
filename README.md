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
aiplus install codex
aiplus memory status
```

Or clone the standalone schemas and templates:

```bash
git clone https://github.com/izhiwen/aiplus-agent-memory.git
cd aiplus-agent-memory
```

See `core/schemas/` for the JSON schemas and `core/templates/` for record templates.
