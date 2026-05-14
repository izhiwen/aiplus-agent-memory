# AiPlus Agent Memory
[![CI](https://github.com/izhiwen/AiPlus-Agent-Memory/actions/workflows/ci.yml/badge.svg)](https://github.com/izhiwen/AiPlus-Agent-Memory/actions/workflows/ci.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

[English README](README.md)

## 痛点

Agent 跨 session 完全失忆。周一你教它项目命名规则，周三它又问你到底要 camelCase 还是 snake_case。到周五，同一个架构决策已经向四个新 session 解释过四遍了。

于是你想：那就**写进 memory 不就完了**。然后你立刻停了——**万一某条记录把你随口说的私人偏好泄漏出去呢？万一上周那个 API key 还留在某段 transcript 里，正好被存进去呢？** 你想让 agent 记住，但不能冒"secret 出现在共享仓库里"的风险。

## 我们的解决方案

AiPlus Agent Memory 是一个**项目本地的 JSONL memory 存储**，让 agent 有真实可用的工作记忆，**同时**让你完全控制什么被记住。

- **记录在你项目里的 `.aiplus/memory/`。** 每个项目自带独立 memory，**不跨项目，不出机器**。
- **写入前跑 12 条 redaction 规则。** 密码、API key、JWT、Authorization header、原始 transcript、provider 请求/响应体、私人路径、个人标识符 —— 都在源头剥掉。**含 secret 的记录会被拒绝**，不是"以后再 redact"。
- **`memory doctor` 自检存储。** Stale 记录、冲突项、schema 违规、孤儿文件 —— 在它们悄悄造成混乱之前就浮上来。
- **被拒绝和被遗忘的记录留在存储里供审计，但默认不进 agent 的 context。** 你保留历史，又不会把坏数据再注回去。

**没有网络调用。没有云同步。没有 vector database。永远不上传。**

## 入门

Memory 是 AiPlus bundle 自带模块。如果你已装好 AiPlus：

```bash
cd MyProject
aiplus add agent-memory       # 加模块（已 bundle 则 no-op）
aiplus install codex          # 或：claude-code, opencode, all
aiplus memory status
```

`aiplus memory status` 告诉你存储有没有初始化、激活记录数多少、`memory doctor` 有没有发现问题。

## 仓库结构

- `core/schemas/` —— memory 记录、identities、skill candidate、audit event、role identity、memory review 的 JSON schema
- `core/templates/` —— 记录模板和角色身份定义（`advisor`、`ceo`、`reviewer`、`builder`）
- `core/docs/` —— memory model、protocol、redaction policy、role identity 参考、skill candidate 生命周期、安全边界、QA checklist
- `adapters/codex/` —— Codex 的 memory 和 identity 命令 skill
- `adapters/claude-code/` —— Claude Code memory 集成
- `adapters/opencode/` —— OpenCode memory prompts 和 agent 配置
- `examples/` —— 安全和被 block 的记录示例、被接受和被拒绝的 skill candidate
- `tests/fixtures/` —— 校验 fixture

## 安全边界

AiPlus Agent Memory **不会**：

- 把 memory 数据或 transcript 上传到任何服务
- 实现云同步或 vector database
- 自动从 transcript 学习（必须显式接受）
- 自动批准 skill candidate（每个都需要显式接受）
- 在记录里存 secret（redaction 在写入前 block）
- 在项目间共享数据（每个项目 memory 独立）

## 贡献

欢迎在插件 scope 内（**项目本地 + 严格 redaction 的 agent memory**，不是通用 vector DB 或云同步 memory）的贡献：

1. **先开 issue** —— 比 typo 大的改动都先开 issue。
2. **每条新 redaction 规则附 fixture** —— 在 `tests/fixtures/` 加测试用例，先有测试再有代码。
3. **保持 adapter 对齐** —— 改 CLI 表面同步更新三个 adapter (`adapters/codex/`, `adapters/claude-code/`, `adapters/opencode/`)。
4. **改完 schema 跑 `aiplus memory doctor`** 验证存储。

## 更多

- 主平台：[AiPlus](https://github.com/izhiwen/AiPlus)
- 下次发布前要跟进的事：
  [AiPlus release notes](https://github.com/izhiwen/AiPlus/releases)

## License

[Apache-2.0](LICENSE)
