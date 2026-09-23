# talkeover

[中文](#中文) | [English](#english)

## 中文

`takeover` 是一个在新会话中恢复项目专注力的 skill。旧对话过长、反复讨论导致重点散失，或因额度耗尽、报错而无法继续时，新会话可以从现有记录重建一份精简工作上下文，回顾关键历史，继续未完成的任务。

handoff 由旧会话主动整理并交出上下文；takeover 由新会话读取历史、自己完成这次交接。两者都希望用更精简的上下文继续工作，takeover 无需旧对话再回复或预先生成交接文档。仓库名为 `talkeover`，安装和调用的 skill 名为 `takeover`。

### 能做什么

- 重建当前目标、关键约束、完成标准、已完成和未完成工作，以及接下来的一步。
- 简短回顾需求如何变化、方案为什么改变、工作停在何处，保留值得记住的失败经验。
- 先建立历史索引，按需读取关键片段；工作摘要保持简短，重复日志和旧代码留在源记录中，需要时再回查。
- 不依赖源会话使用的模型、工具名称或工具调用格式；历史工具记录只作为需要核实的证据。
- 将旧项目路径核实并映射到当前项目，避免把相同文件名误判为同一文件。
- 保留当前会话的权限、系统指令与项目规则；不会自动重放历史命令、发布或发送消息。

### 安装

克隆仓库后，把 skill 放到 Codex 的 skills 目录：

```bash
git clone https://github.com/awangs1986/talkeover.git
cd talkeover
mkdir -p ~/.codex/skills
ln -s "$(pwd)/takeover" ~/.codex/skills/takeover
```

请在仓库根目录执行最后一行。若不想使用符号链接，也可以复制 `takeover/` 目录到 `~/.codex/skills/takeover/`。重启或新建 Codex 会话后即可使用。

### 使用方式

```text
$takeover 原对话 ID，在当前项目继续剩下的工作
$takeover 原对话 ID，旧对话额度用完了；简短回顾历史，重新聚焦当前任务并继续
$takeover /path/to/session.jsonl，把旧项目 /old/repo 映射到当前目录
$takeover /path/to/export.json，只接手其中的登录功能；先核对当前实现
$takeover https://example.com/session.json，先恢复上下文，暂不修改代码
```

### 示例：旧对话额度耗尽，任务还没完成

以下为虚构的使用示例，不是实际测试结果。旧对话 A 要修复支付回调重复扣款：最初讨论了 Redis 去重，后来用户要求不引入新服务，改为数据库唯一约束。历史记录显示已加约束、单次回调测试通过，但并发测试发出后未返回结果，随后额度耗尽。没有生成 handoff 文档。

已有导出 `/archive/payment-fix.jsonl`，其中的项目目录是 `/Users/alice/work/shop`。在 `/home/me/projects/shop` 打开新对话 B：

```text
$takeover /archive/payment-fix.jsonl，旧对话额度用完了。把 /Users/alice/work/shop 映射到当前目录，简短回顾历史后继续修复支付回调。
```

期望得到的接手摘要如下（实际内容必须以读取记录和核实项目的结果为准）：

> **当前目标：** 修复重复扣款；不引入新服务；并发重复回调只产生一次扣款。
>
> **历史回顾：** 最初考虑 Redis → 用户要求避免新服务 → 改用数据库唯一约束 → 记录显示单次测试通过 → 并发测试没有结果，旧对话中断。
>
> **当前状态：** 源记录声称约束已添加，需核对当前代码及迁移；历史测试通过不代表当前项目已验证。
>
> **下一步：** 先核对当前项目的迁移和处理逻辑，再验证并发重复请求；无需重新讨论已放弃的 Redis 方案。
>
> **路径与来源：** 将旧项目根目录映射到当前目录，逐项核实相关文件；保留关键消息位置和未返回的测试记录供回查。

旧对话不需要再运行一次。新会话使用当前可用工具推进任务，并在需要时查阅历史证据。

### 能力边界

这是让代理执行的工作流程，不是独立的会话导入程序。需要可读取的源记录和仍可运行的新会话；只有 ID 而没有记录访问权限无法恢复内容。它不绕过账户额度，也不能清空已经加载的上下文，因此建议在新会话调用，并避免全量复制旧日志。跨模型指不依赖原模型和工具协议，不保证所有模型的理解质量相同。

## English

`takeover` rebuilds focused working context in a fresh conversation. Use it when a long discussion has lost focus, or when the original conversation cannot continue because of exhausted quota, an error, or an interruption. The new conversation reads existing records, recalls the important history, and resumes unfinished work.

A handoff is prepared by the outgoing conversation; a takeover is reconstructed by the incoming conversation. Both aim to continue with concise context. Takeover requires no further response or handoff document from the old conversation. The repository is named `talkeover`; the installed skill and invocation are named `takeover`.

### What it does

- Reconstructs the current goal, constraints, completion criteria, progress, remaining work, and immediate next step.
- Briefly recalls requirement changes, decision reasons, useful failed attempts, and the interruption point.
- Builds a history index and retrieves relevant passages as needed. Keeps the working summary small; repetitive logs and superseded code stay in the source records.
- Works across models and tool protocols. Historical tool records are evidence to verify, never calls to replay.
- Verifies and maps old project paths to the current project instead of assuming matching filenames are equivalent.
- Keeps the current session's permissions, instructions, and project rules. It does not automatically replay commands, publish, or send messages.

### Install

Clone the repository, then make the skill available to Codex:

```bash
git clone https://github.com/awangs1986/talkeover.git
cd talkeover
mkdir -p ~/.codex/skills
ln -s "$(pwd)/takeover" ~/.codex/skills/takeover
```

Run the last command from the cloned repository root. You may copy `takeover/` to `~/.codex/skills/takeover/` instead of creating a symlink. Start a new Codex session after installation.

### Usage

```text
$takeover <source conversation ID>; continue the remaining work in this project
$takeover <source conversation ID>; the old chat ran out of quota. Briefly recap the history, refocus, and continue
$takeover /path/to/session.jsonl; map /old/repo to the current directory
$takeover /path/to/export.json; resume only the login feature and verify the current implementation first
$takeover https://example.com/session.json; recover context without changing code yet
```

### Example: quota exhausted before the task was finished

This is a fictional usage example, not a test result. Conversation A was fixing duplicate payment charges. It initially considered Redis, but the user ruled out adding a service, so the approach changed to a database uniqueness constraint. The history claims the constraint was added and a single-request test passed. A concurrent-request test had no recorded result when quota ran out. No handoff was prepared.

An existing export is at `/archive/payment-fix.jsonl`, with the old project at `/Users/alice/work/shop`. Open conversation B in `/home/me/projects/shop` and say:

```text
$takeover /archive/payment-fix.jsonl; the old chat ran out of quota. Map /Users/alice/work/shop to the current directory, briefly recap the history, and continue the payment-callback fix.
```

An expected recap would look like this; actual claims must follow the available records and project checks:

> **Goal:** Prevent duplicate charges without a new service; concurrent duplicate callbacks must produce one charge.
>
> **History:** Redis considered → user ruled out new services → database uniqueness chosen → single-request test reportedly passed → concurrent test has no result; conversation interrupted.
>
> **State:** The source claims the constraint was added; check the current code and migration. Historical test success is not current verification.
>
> **Next step:** Inspect the migration and handler, then verify concurrent duplicates; do not reopen the rejected Redis approach without a new reason.
>
> **Paths and sources:** Map the old project root to the current directory and verify the relevant files; retain message locations and the unfinished test record for reference.

The old conversation does not need to run again. The new conversation uses currently available tools and consults historical evidence only when needed.

### Limits

This is an agent workflow, not a standalone session importer. It needs readable source records and a working destination conversation; an ID alone cannot recover inaccessible content. It does not bypass account quotas or clear already-loaded context. Invoke it in a fresh conversation and avoid copying the entire old log. Cross-model support means independence from the original model and tool protocol, not identical interpretation quality across models.

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
