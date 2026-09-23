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
- 用由大到小的 10 个问题和已整理的答案确认项目画像；有分歧时集中澄清并确认修订 SPEC，再继续实施。
- 保留当前会话的权限、系统指令与项目规则；不会自动重放历史命令、发布或发送消息。

### 接手后先确认理解

10 个问题依次覆盖：项目方向 → 使用者 → 核心交付 → 本次范围 → 关键约束 → 历史转折 → 当前方案 → 实际进展 → 接续重点 → 验收标准。每题附上代理从记录中整理的简短理解，无依据处标为未确认，用户无需重新描述整个项目。

你可以直接回复“认可”，也可以说“第 4 和第 7 点不对……”并纠正；只回答部分项目不会被当作整体认可。确认前仅进行只读检查和草稿整理，确认后才继续实施。

如有异议，会自动采用 `grill-with-docs` 的思路：围绕原有 10 个主题澄清关键分歧、核对术语并记录决策。明确的纠正直接采纳；有必要时一次只追问一个关键点，不另开长问卷，也不反复问已认可的内容。随后展示修订 SPEC 及变化，等你确认后继续。理解纠错与需求变更会分别记录；已有 SPEC 只调整受影响部分。

这套流程已包含在 takeover 中，无需安装 `grill-with-docs` 或其依赖。没有分歧时，认可画像即可继续，不额外要求一轮 SPEC 确认。

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

期望看到以下 10 个确认问题（实际内容必须以读取记录和核实项目的结果为准）：

1. **项目要带来什么价值？** 我的理解：目前可确认的目标是支付可靠性，项目整体定位尚未从记录确认。
2. **主要服务谁？** 我的理解：涉及支付流程的用户，具体用户群待确认。
3. **核心交付是什么？** 我的理解：可靠处理重复支付回调。
4. **本次做到哪里？** 我的理解：先完成支付回调修复，其他支付功能未纳入本次范围。
5. **必须遵守什么约束？** 我的理解：不引入新的服务。
6. **为何走到当前方案？** 我的理解：Redis 方案因新增服务被放弃，改为数据库约束，并发测试未返回时对话中断。
7. **采用什么做法？** 我的理解：以数据库唯一约束为核心，细节需要与当前实现核对。
8. **现在完成到哪里？** 我的理解：记录声称已加约束、单次测试通过，当前项目仍需验证，并发结果未知。
9. **先继续哪一步？** 我的理解：核对映射到当前目录的迁移和处理逻辑，再验证并发重复请求。
10. **怎样算完成？** 我的理解：并发重复回调只产生一次扣款，并有验证结果支持。

> 以上 10 点是否准确？可以直接回复认可，或指出不对的编号和你的修正。

如果你回复“第 5 点补充一下，可以新增数据库表，但不能新增服务，其他认可”，这条纠正已足够清楚，无需重新盘问。代理会将该约束写入修订 SPEC、保留其他已确认部分，展示草稿供你确认；确认后继续。未知事实依旧需要核实，整体认可不会把它们变成已知。

旧对话不需要再运行一次。新会话在确认后使用当前工具推进任务，并按需查阅历史证据。

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
- Confirms a project portrait through 10 questions from broad direction to execution details; disagreements lead to focused clarification and an approved revised spec before implementation resumes.
- Keeps the current session's permissions, instructions, and project rules. It does not automatically replay commands, publish, or send messages.

### Confirm understanding before continuing

The 10 questions cover: project purpose → users → core deliverable → current scope → constraints → historical turning points → chosen approach → actual progress → next priority → acceptance criteria. Each comes with the agent's brief understanding from the records. Unknowns are marked; you do not have to describe the project again.

Reply “Agreed” to confirm the portrait, or correct individual numbered items. A partial answer is not blanket approval. Before confirmation, the agent only performs read-only checks and prepares drafts; implementation follows confirmation.

Disagreements automatically trigger the `grill-with-docs` approach: clarify material differences within the same 10 topics, check terminology, and record decisions. Clear corrections are accepted directly. When clarification is needed, ask one essential question at a time without opening another questionnaire or revisiting accepted answers. The agent then presents a revised spec and its changes for your approval before continuing. Misunderstandings and changed requirements are recorded separately; an existing spec is revised only where needed.

This workflow is built into takeover and does not require `grill-with-docs` or its dependencies. When there is no disagreement, confirming the portrait is sufficient; no additional spec approval is introduced.

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

An expected portrait asks these 10 questions; actual claims must follow the records and project checks:

1. **What value should the project deliver?** My understanding: payment reliability is established; the overall project purpose is not yet confirmed.
2. **Who uses it?** My understanding: users of the payment flow; the specific audience is unconfirmed.
3. **What is the core deliverable?** My understanding: reliable handling of duplicate payment callbacks.
4. **What is the current scope?** My understanding: finish the callback fix; other payment features are outside this task.
5. **What constraints apply?** My understanding: do not introduce a new service.
6. **How did we reach this approach?** My understanding: Redis was rejected because it added a service; database uniqueness was selected; the conversation stopped without a concurrent-test result.
7. **What approach was chosen?** My understanding: a database uniqueness constraint is central; implementation details need verification.
8. **What is actually complete?** My understanding: the source claims a constraint and a passing single-request test; the current project remains unverified and concurrency results are unknown.
9. **What should happen next?** My understanding: inspect the mapped migration and handler, then verify concurrent duplicates.
10. **What counts as done?** My understanding: concurrent duplicate callbacks produce one charge, supported by verification results.

> Are these 10 points accurate? You can confirm them together or correct specific numbered items.

Suppose you reply, “Clarify point 5: adding a database table is fine, but adding a service is not; I agree with the rest.” That is a clear correction, so no further interview is needed. The agent includes it in a revised spec, preserves the other confirmed points, and presents the draft for approval before continuing. Unknown facts still require checking; approval does not make them known.

The old conversation does not need to run again. After confirmation, the new conversation uses current tools and consults historical evidence only when needed.

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
