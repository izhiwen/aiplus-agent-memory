# AiPlus Agent Memory

[English README](README.md)

## 痛点

周一你教了 agent 项目的命名规范，周三它就忘了。你加了一条 memory，又担心里面不小心粘贴了私人偏好或 API key 片段。你希望 agent 记住，但不想把 secret 泄漏到 public repository。

## 解决方案

AiPlus Agent Memory 把项目级记录以 JSONL 形式存在 `.aiplus/memory/` 下。任何记录写入前，十二条 redaction 模式自动剥除 password、JWT、raw transcript 等敏感串。`memory doctor` 自检 stale records、conflicts 和 schema violations。被拒绝或已忘记的记录留在 store 里，但默认不进入上下文。全部本地运行，不上传，不出本机。

## 快速开始

如果你已安装 AiPlus，安装任意模块时 memory 自动启用：

```bash
cd MyProject
aiplus install codex        # 或: claude-code, opencode, all
aiplus memory status
```

或 clone standalone schema 和模板：

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

每个 runtime 获得项目级 adapter 文件，启用自然语言记忆命令。

## 内部结构

- `core/schemas/` — memory-record、identity、skill-candidate、audit-event 的 JSON schema
- `core/templates/` — 记忆记录和身份模板
- `core/docs/` — 记忆模型、协议、redaction 策略、角色身份文档
- `adapters/codex/` — Codex memory 命令 skills
- `adapters/claude-code/` — Claude Code memory 集成
- `adapters/opencode/` — OpenCode memory prompts
- `examples/` — Safe 和 blocked 记忆记录示例

## 常用命令

```bash
aiplus memory init --project              # 初始化项目记忆
aiplus memory status                      # 显示记忆记录数
aiplus memory doctor                      # 运行记忆健康检查
aiplus memory context --runtime codex     # 构建上下文包
aiplus memory add --scope project         # 添加项目记忆
aiplus memory search "naming"             # 搜索记忆
aiplus memory forget <id>                 # 忘记一条记忆
aiplus identity init --project            # 初始化角色身份
aiplus identity context --role advisor    # 加载顾问上下文
```

## 安全边界

AiPlus Agent Memory 不：
- 上传记忆数据或 transcript
- 实现 cloud sync 或 vector database
- 自动从 transcript 学习
- 自动批准 skills
- 在记忆记录中存储 secrets（redaction 会阻止）

## 路线图

见 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md) 了解当前缺口。

## License

[Apache-2.0](LICENSE)
