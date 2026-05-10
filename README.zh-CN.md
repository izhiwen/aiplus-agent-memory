# AiPlus Agent Memory

[English README](README.md)

## 问题所在

周一早上，你花了二十分钟教 agent 项目的命名规范。到周三 agent 已经忘了该用 camelCase 还是 snake_case。你加了 memory 文件帮忙，又担心里面不小心粘贴了上周的私人偏好或 API key 片段。你希望 agent 记住，但不能冒险把 secret 泄漏到 public repository。

## 它能做什么

AiPlus Agent Memory 把项目规范以 JSONL 记录形式存在 `.aiplus/memory/` 下。每条记录包含记忆项、角色身份或 skill candidate 提案。任何记录写入前，十二条自动 redaction 模式剥除敏感串：

- Password 和 API key
- JWT token 和 auth header
- Raw transcript 和 provider request/response body
- 私人路径和个人标识符

`memory doctor` 命令运行自动化健康检查：stale records、条目间 conflicts、schema violations 和 orphan files。被拒绝或已忘记的记录留在 store 中供审计，但默认对 agent 上下文隐藏。

角色身份定义 agent 该如何表现：Advisor 负责策略，CEO 负责规划，Reviewer 负责审阅，Builder 负责实现。用自然语言命令切换角色。

## 安装

已安装 AiPlus 时，memory 自动启用：

```bash
cd MyProject
aiplus install codex        # 或: claude-code, opencode, all
aiplus memory status
```

或使用 standalone schema 和模板：

```bash
git clone https://github.com/izhiwen/aiplus-agent-memory.git
cd aiplus-agent-memory
```

## Runtime 支持

Memory 支持所有三种 agent。为对应 runtime 安装 AiPlus：

```bash
aiplus install codex        # Codex
aiplus install claude-code  # Claude Code
aiplus install opencode     # OpenCode
```

每个 runtime 获得项目级 adapter 文件，在 agent session 中启用自然语言记忆命令。

## 工作原理

agent session 中的自然语言命令映射到记忆操作：

```text
记住函数要用 camelCase                → aiplus memory add
你记得这个项目哪些规范？              → aiplus memory status
切换到 advisor 模式                   → aiplus identity context --role advisor
忘掉那个关于制表符的规则              → aiplus memory forget <id>
```

## 仓库结构

- `core/schemas/` — memory-record、identity、skill-candidate、audit-event、role-identity 的 JSON schema
- `core/templates/` — 记忆记录模板和角色身份定义（advisor、ceo、reviewer、builder）
- `core/docs/` — 记忆模型、协议、redaction 策略、角色身份、skill candidate 生命周期、安全边界
- `adapters/codex/` — Codex 记忆和身份命令 skills
- `adapters/claude-code/` — Claude Code 记忆集成和命令
- `adapters/opencode/` — OpenCode 记忆 prompts 和 agents
- `examples/` — Safe 和 blocked 记忆记录示例，展示 redaction 效果
- `tests/` — 测试 fixtures 和验证用例

## 命令

```bash
# 记忆管理
aiplus memory init --project              # 初始化项目记忆 store
aiplus memory status                      # 显示记录数和健康状态
aiplus memory doctor                      # 运行自动化健康检查
aiplus memory context --runtime codex     # 为 agent 构建上下文包
aiplus memory add --scope project         # 添加项目级记忆
aiplus memory search "naming"             # 搜索记忆记录
aiplus memory forget <id>                 # 从上下文移除记录

# 身份管理
aiplus identity init --project            # 初始化角色身份
aiplus identity context --role advisor    # 加载 advisor 上下文
aiplus identity context --role ceo        # 加载 CEO 上下文

# Skill candidates
aiplus skill-candidate status             # 显示 proposed skills
```

## 安全

AiPlus Agent Memory 不：
- 上传记忆数据或 transcript 到任何服务
- 实现 cloud sync 或 vector database
- 未经显式批准自动从 transcript 学习
- 自动批准 skills（candidates 需要显式接受）
- 在记忆记录中存储 secrets（redaction 在写入前阻止）
- 在项目间共享数据（每个项目有隔离的记忆）

## 更多信息

见 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus) 了解完整平台。

当前缺口和计划工作：[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)。

## License

[Apache-2.0](LICENSE)
