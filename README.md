# talkeover

[中文](#中文) | [English](#english)

## 中文

`takeover` 是一个 Codex skill，用来把另一段对话的工作接到当前会话和当前项目中继续完成。它与导出摘要的 handoff 相反：读取原对话 ID、会话链接、JSON/JSONL 导出或交接文档，提炼可以执行的任务状态，而不是重放旧对话。

### 能做什么

- 提取最终目标、有效约束、已完成和未完成工作、验证证据与阻塞点。
- 不依赖源会话使用的模型、工具名称或工具调用格式；历史工具记录只作为需要核实的证据。
- 将旧项目路径核实并映射到当前项目，避免把相同文件名误判为同一文件。
- 保留当前会话的权限、系统指令与项目规则；不会自动重放历史命令、发布或发送消息。

### 安装

克隆仓库后，把 skill 放到 Codex 的 skills 目录：

```bash
git clone https://github.com/awangs1986/talkeover.git
mkdir -p ~/.codex/skills
ln -s "$(pwd)/talkeover/takeover" ~/.codex/skills/takeover
```

请在仓库根目录执行最后一行。若不想使用符号链接，也可以复制 `takeover/` 目录到 `~/.codex/skills/takeover/`。重启或新建 Codex 会话后即可使用。

### 使用方式

```text
$takeover 原对话 ID，在当前项目继续剩下的工作
$takeover /path/to/session.jsonl，把旧项目 /old/repo 映射到当前目录
$takeover /path/to/export.json，只接手其中的登录功能；先核对当前实现
$takeover https://example.com/session.json，先恢复上下文，暂不修改代码
```

### 示例

之前的会话导出在 `/archive/payment-fix.jsonl`，其中的项目目录是 `/Users/alice/work/shop`。现在你在 `/home/me/projects/shop` 打开 Codex：

```text
$takeover /archive/payment-fix.jsonl，把 /Users/alice/work/shop 映射到当前目录，继续修复支付回调
```

skill 会找出最后确认的支付问题、已经修改过的文件与测试结果，核实它们是否在当前项目存在，并把例如 `/Users/alice/work/shop/src/webhook.ts` 映射到 `/home/me/projects/shop/src/webhook.ts`。它不会重放旧对话里的命令或沿用旧模型；之后会用当前环境的工具完成剩余工作。

## English

`takeover` is a Codex skill for resuming work from another conversation in the current chat and project. It is the inverse of a handoff export: it reads a source conversation ID, session link, JSON/JSONL export, or handoff document and turns the useful history into an actionable task state.

### What it does

- Extracts the final goal, applicable constraints, completed and remaining work, evidence, and blockers.
- Works across models and tool protocols. Historical tool records are evidence to verify, never calls to replay.
- Verifies and maps old project paths to the current project instead of assuming matching filenames are equivalent.
- Keeps the current session's permissions, instructions, and project rules. It does not automatically replay commands, publish, or send messages.

### Install

Clone the repository, then make the skill available to Codex:

```bash
git clone https://github.com/awangs1986/talkeover.git
mkdir -p ~/.codex/skills
ln -s "$(pwd)/talkeover/takeover" ~/.codex/skills/takeover
```

Run the last command from the cloned repository root. You may copy `takeover/` to `~/.codex/skills/takeover/` instead of creating a symlink. Start a new Codex session after installation.

### Usage

```text
$takeover <source conversation ID>; continue the remaining work in this project
$takeover /path/to/session.jsonl; map /old/repo to the current directory
$takeover /path/to/export.json; resume only the login feature and verify the current implementation first
$takeover https://example.com/session.json; recover context without changing code yet
```

### Example

Suppose an exported conversation is at `/archive/payment-fix.jsonl` and its project was `/Users/alice/work/shop`. You open Codex in `/home/me/projects/shop` and say:

```text
$takeover /archive/payment-fix.jsonl; map /Users/alice/work/shop to the current directory and continue the payment-callback fix
```

The skill identifies the last confirmed payment issue, claimed edits, and test results; checks whether they exist in the current project; and maps `/Users/alice/work/shop/src/webhook.ts` to `/home/me/projects/shop/src/webhook.ts`. It does not replay old commands or adopt the old model. It uses the current environment to complete the remaining work.

## Repository layout

```text
takeover/
├── README.md
└── takeover/
    ├── SKILL.md
    └── agents/openai.yaml
```

## License

MIT
