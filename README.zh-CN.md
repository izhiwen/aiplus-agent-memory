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
aiplus install codex
aiplus memory status
```

或 clone standalone schema 和模板：

```bash
git clone https://github.com/izhiwen/aiplus-agent-memory.git
cd aiplus-agent-memory
```

JSON schema 见 `core/schemas/`，记录模板见 `core/templates/`。
