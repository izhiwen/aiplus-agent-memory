# AiPlus Agent Memory

[English](README.md)

## The Problem

你的 agent 在会话之间患有彻底的失忆症（amnesia）。周一你教完命名规范，
周三它已经忘了该用 camelCase 还是 snake_case。同样的偏好你一周要重复教
三遍。

往记忆里加东西让人不安。如果某条记录泄露了你随口提过的私人偏好怎么办？
如果上周的 API key 片段还留在某条被存储的 transcript 里怎么办？你希望
agent 记得，但你不能冒险让秘密出现在共享仓库中。

## The Solution

AiPlus Agent Memory 把项目规范以 JSONL 记录形式存放在
`.aiplus/memory/` 下。每条记录都隶属于当前项目，不会离开本机。

任何记录写入前，十二条自动脱敏（redaction）规则会先剥除内容中的敏感
字符串：

- 密码与 API key
- JWT token 与 authorization header
- 原始 transcript 与 provider 的请求/响应体
- 私人文件路径与个人标识符

`memory doctor` 命令会对存储运行自检：过期记录、冲突条目、schema
违规与孤儿文件。被拒绝或已遗忘的记录仍留在存储中供审计，但默认对
agent 的上下文隐藏。

没有网络请求，没有云端同步，没有向量数据库，也没有上传。

## Quick Start

安装项目 agent 时，AiPlus 会自动启用 memory：

```bash
cd MyProject
aiplus install opencode     # 或: codex, claude-code
aiplus memory status
```

`aiplus memory status` 会输出存储是否已初始化、活跃记录数量，以及
`memory doctor` 是否发现问题。

## What's Inside

关键目录与文件：

- `core/schemas/` — memory record、identity、skill candidate、
  audit event、role identity、memory review 的 JSON schema
- `core/templates/` — 记忆记录模板与角色身份定义（`advisor`、`ceo`、
  `reviewer`、`builder`）
- `core/docs/` — 记忆模型、协议、脱敏策略、角色身份参考、skill
  candidate 生命周期、安全边界与 QA 检查清单
- `adapters/codex/` — Codex 的记忆与身份命令 skill
- `adapters/claude-code/` — Claude Code 的记忆集成
- `adapters/opencode/` — OpenCode 的记忆 prompt 与 agent 配置
- `examples/` — 安全与被拦截的记忆记录示例，以及已拒绝和已接受的
  skill candidate
- `tests/fixtures/` — 验证固件

## Safety Boundaries

AiPlus Agent Memory 不会：

- 上传记忆数据或 transcript 到任何服务
- 实现云端同步或向量数据库
- 未经显式批准自动从 transcript 学习
- 自动批准 skill（candidate 需要显式接受）
- 在记忆记录中存储 secrets（redaction 在写入前阻止）
- 在项目间共享数据（每个项目拥有隔离的记忆）

## More Info

- 主平台：[aiplus](https://github.com/izhiwen/aiplus)
- 当前缺口与计划工作：
  [v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
