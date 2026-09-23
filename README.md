# talkeover

[中文](#中文) | [English](#english)

## 中文

`takeover` 是一个不绑定特定代理、模型或平台的通用 skill，用于在新会话中恢复项目专注力。旧对话过长、反复讨论导致重点散失，或因额度耗尽、报错而无法继续时，新会话可以从现有记录重建一份精简工作上下文，回顾关键历史，继续未完成的任务。

handoff 由旧会话主动整理并交出上下文；takeover 由新会话读取历史、自己完成这次交接。两者都希望用更精简的上下文继续工作，takeover 无需旧对话再回复或预先生成交接文档。仓库名为 `talkeover`，安装和调用的 skill 名为 `takeover`。

### 能做什么

- 重建当前目标、关键约束、完成标准、已完成和未完成工作，以及接下来的一步。
- 简短回顾需求如何变化、方案为什么改变、工作停在何处，保留值得记住的失败经验。
- 先建立历史索引，按需读取关键片段；工作摘要保持简短，重复日志和旧代码留在源记录中，需要时再回查。
- 不依赖源会话使用的模型、工具名称或工具调用格式；历史工具记录只作为需要核实的证据。
- 将旧项目路径核实并映射到当前项目，避免把相同文件名误判为同一文件。
- 用尽可能少的问题校验接手理解，最多 10 题；有分歧时先回查历史，再做必要澄清并确认修订 SPEC，之后继续实施。
- 保留当前会话的权限、系统指令与项目规则；不会自动重放历史命令、发布或发送消息。

### 接手后先确认理解

问题用于检验代理是否接住原始上下文，理解和整理工作由代理完成。10 题是整个接手过程初次核对与后续实质澄清的上限，不是必须问满的数量；能用一份简短理解和一次整体确认完成，就只问一次。已从历史明确的信息直接概括，不要求用户重答。

你可以直接回复“认可”，也可以指出摘要中不对的地方；只回答部分问题不会被当作整体认可。确认前仅进行只读检查和草稿整理，确认后才继续实施。

如果需要问很多问题，代理应先回查原始上下文，检查是否漏读或误解，不能让用户重新梳理项目来补偿恢复不足。只有回查后仍影响下一步的缺口才需要问；资料确实无法取得时会说明具体缺口，不靠凑问题或猜测继续。

有异议时，借用 `grill-with-docs` 的核实分歧、澄清术语和记录决策的思路，先查历史，明确的纠正直接采纳，只对剩余关键歧义做最少追问。不另开需求访谈，也不反复问已认可内容。随后以原上下文和既有 SPEC 为基础展示修订内容，等你确认后继续；代理的理解错误不会被当作用户改变需求。

这套流程已包含在 takeover 中，无需安装 `grill-with-docs` 或其依赖。没有分歧时，认可画像即可继续，不额外要求一轮 SPEC 确认。

### 安装

核心是全英文的 [`takeover/SKILL.md`](takeover/SKILL.md)，不依赖特定工具 API 或插件。先获取仓库：

```bash
git clone https://github.com/awangs1986/talkeover.git
cd talkeover
```

支持 skills 的代理：将 `takeover/` 目录复制或链接到该代理配置的技能目录，并按其机制加载。默认目录示例：Codex 为 `~/.codex/skills/takeover/`，Pi 为 `~/.pi/agent/skills/takeover/`；自定义配置和其他代理以实际设置为准。

不支持 skills 的代理：将 `SKILL.md` 作为任务说明提供，并附上可读取的源对话导出。没有本地文件或会话查询能力时，可以使用当前应用支持的附件或文本输入；仅提供另一个应用的对话 ID 不代表当前应用就能读取它。

`agents/openai.yaml` 仅是可选的 Codex 界面和调用策略适配，其他代理无需加载；完整工作流程都在 `SKILL.md` 中。不同代理的安装、加载和工具权限仍由各自环境决定。

### 使用方式

加载 skill 后，用当前代理的调用方式，或直接说明：

```text
使用 takeover 接手原对话 ID，确认理解后在当前项目继续剩下的工作
使用 takeover 读取附上的对话导出；旧对话额度用完了，先简短回顾历史并恢复当前任务
使用 takeover 读取 /path/to/session.jsonl，把旧项目 /old/repo 映射到当前目录
使用 takeover 读取 /path/to/export.json，只接手其中的登录功能；先核对当前实现
使用 takeover 读取 https://example.com/session.json，先恢复上下文，暂不修改代码
```

支持 `$takeover` 的宿主可以使用该快捷方式；它不是通用流程的必要语法。项目描述最多 10 句，能少则少，简单项目一两句即可；确认和澄清问题也只在必要时提出。
### 示例：旧对话额度耗尽，任务还没完成

以下为虚构的使用示例，不是实际测试结果。旧对话 A 要修复支付回调重复扣款：最初讨论了 Redis 去重，后来用户要求不引入新服务，改为数据库唯一约束。历史记录显示已加约束、单次回调测试通过，但并发测试发出后未返回结果，随后额度耗尽。没有生成 handoff 文档。

已有导出 `/archive/payment-fix.jsonl`，其中的项目目录是 `/Users/alice/work/shop`。在 `/home/me/projects/shop` 打开新对话 B：

```text
使用 takeover 读取 /archive/payment-fix.jsonl，旧对话额度用完了。把 /Users/alice/work/shop 映射到当前目录，简短回顾历史并确认理解后继续修复支付回调。
```

代理先整理已有信息，只需一次整体确认（实际内容必须以读取记录和核实项目的结果为准）：

1. **目标与约束：** 修复支付回调重复扣款，不引入新服务；并发重复回调只应产生一次扣款。
2. **历史与方案：** Redis 因需新增服务而被放弃，改为数据库唯一约束；记录声称约束已添加、单次测试通过，并发测试无结果时旧对话中断。
3. **停点与接续：** 当前实现和历史测试结论仍需核实；确认理解后核对映射到当前目录的迁移和处理逻辑，再验证并发重复请求。

> 以上理解是否准确？可以直接回复认可，或指出不对的地方。

如果你回复“第 1 点补充一下，可以新增数据库表，但不能新增服务，其他认可”，纠正已经清楚，无需重新盘问。代理会回查相关约束，将澄清记录在修订 SPEC 中、保留其他有效内容，展示草稿供你确认后继续。与当前修复无关的产品画像问题不会为了凑数被提出；代码和测试是否存在则由代理自己核实。

旧对话不需要再运行一次。新会话在确认后使用当前工具推进任务，并按需查阅历史证据。

### 能力边界

这是让代理执行的工作流程，不是独立的会话导入程序。需要可读取的源记录和仍可运行的新会话；只有 ID 而没有记录访问权限无法恢复内容。它不绕过账户额度，也不能清空已经加载的上下文，因此建议在新会话调用，并避免全量复制旧日志。通用指核心流程不绑定平台，不代表已逐一验证所有代理，或能跨应用自动获得数据权限；实际读取、路径验证和执行能力取决于当前环境。跨模型不保证所有模型的理解质量相同。

## English

`takeover` is a general-purpose skill independent of any particular agent, model, or platform. It rebuilds focused working context in a fresh conversation when a long discussion has lost focus or the original conversation cannot continue because of exhausted quota, an error, or an interruption. The new conversation reads existing records, recalls the important history, and resumes unfinished work.

A handoff is prepared by the outgoing conversation; a takeover is reconstructed by the incoming conversation. Both aim to continue with concise context. Takeover requires no further response or handoff document from the old conversation. The repository is named `talkeover`; the installed skill and invocation are named `takeover`.

### What it does

- Reconstructs the current goal, constraints, completion criteria, progress, remaining work, and immediate next step.
- Briefly recalls requirement changes, decision reasons, useful failed attempts, and the interruption point.
- Builds a history index and retrieves relevant passages as needed. Keeps the working summary small; repetitive logs and superseded code stay in the source records.
- Works across models and tool protocols. Historical tool records are evidence to verify, never calls to replay.
- Verifies and maps old project paths to the current project instead of assuming matching filenames are equivalent.
- Checks the recovered understanding with as few questions as possible, at most 10; disagreements prompt a source recheck, minimal clarification, and approval of a revised spec before implementation resumes.
- Keeps the current session's permissions, instructions, and project rules. It does not automatically replay commands, publish, or send messages.

### Confirm understanding before continuing

Questions check whether the agent has recovered the original context; the agent does the reconstruction. Ten is the limit across the initial check and substantive follow-up clarification, not a target. If a brief recap and one overall confirmation suffice, ask only once. Summarize established facts instead of asking the user to supply them again.

Reply “Agreed” to confirm the recap, or correct anything inaccurate. A partial answer is not blanket approval. Before confirmation, the agent only performs read-only checks and prepares drafts; implementation follows confirmation.

If many questions seem necessary, the agent first revisits the source to look for missed or misunderstood context. The user should not have to reconstruct the project to compensate for incomplete recovery. Ask only about remaining gaps that affect the next step; if records are unavailable, identify the specific missing information rather than guessing or padding a questionnaire.

For disagreements, borrow the `grill-with-docs` principles of checking differences, clarifying terms, and recording decisions: recheck history, accept clear corrections, and ask only the minimum needed about material ambiguities. Do not reopen requirements discovery or revisit accepted answers. Present revisions based on the original context and existing spec for approval before continuing; an agent's misunderstanding is not a user-requested requirements change.

This workflow is built into takeover and does not require `grill-with-docs` or its dependencies. When there is no disagreement, confirming the portrait is sufficient; no additional spec approval is introduced.

### Install

The complete workflow is in the English [`takeover/SKILL.md`](takeover/SKILL.md), with no required tool API or plugin. First obtain the repository:

```bash
git clone https://github.com/awangs1986/talkeover.git
cd talkeover
```

For agents with skill support, copy or link `takeover/` into the host's configured skills directory and load it using that host's mechanism. Default directory examples are `~/.codex/skills/takeover/` for Codex and `~/.pi/agent/skills/takeover/` for Pi; follow actual settings for custom configurations and other agents.

For agents without a skill loader, provide `SKILL.md` as task instructions together with a readable source export. If local files or conversation lookup are unavailable, use the application's supported attachments or text input. Supplying an ID from another application does not itself grant access to that conversation.

`agents/openai.yaml` is optional Codex UI and invocation-policy metadata; other agents do not need to load it. The complete workflow is in `SKILL.md`. Installation, loading, and tool permissions remain host-specific.

### Usage

After making the skill available, use the host's invocation mechanism or ordinary instructions:

```text
Use takeover with conversation ID [source ID]; continue in this project after confirming your understanding
Use takeover with the attached export; the old chat ran out of quota. Briefly recap the history and recover the current task
Use takeover with /path/to/session.jsonl; map /old/repo to the current directory
Use takeover with /path/to/export.json; resume only the login feature and verify the current implementation first
Use takeover with https://example.com/session.json; recover context without changing code yet
```

Hosts supporting `$takeover` may use that shortcut; it is not required syntax. Describe the project in at most 10 sentences, fewer whenever possible; a simple project may need one or two. Ask confirmation and clarification questions only as needed.
### Example: quota exhausted before the task was finished

This is a fictional usage example, not a test result. Conversation A was fixing duplicate payment charges. It initially considered Redis, but the user ruled out adding a service, so the approach changed to a database uniqueness constraint. The history claims the constraint was added and a single-request test passed. A concurrent-request test had no recorded result when quota ran out. No handoff was prepared.

An existing export is at `/archive/payment-fix.jsonl`, with the old project at `/Users/alice/work/shop`. Open conversation B in `/home/me/projects/shop` and say:

```text
Use takeover with /archive/payment-fix.jsonl; the old chat ran out of quota. Map /Users/alice/work/shop to the current directory, briefly recap the history, confirm your understanding, and continue the payment-callback fix.
```

The agent summarizes what is known and requests one overall confirmation; actual claims must follow the records and project checks:

1. **Goal and constraints:** Prevent duplicate charges from payment callbacks without introducing a new service; concurrent duplicate callbacks should produce one charge.
2. **History and approach:** Redis was rejected because it required a new service, so database uniqueness was chosen; the records claim a constraint was added and a single-request test passed, but the conversation stopped without a concurrent-test result.
3. **Stopping point and continuation:** The current implementation and historical test claims still need verification; after confirming understanding, inspect the mapped migration and handler, then verify concurrent duplicates.

> Is this understanding accurate? You can confirm it or correct anything inaccurate.

Suppose you reply, “Clarify point 1: adding a database table is fine, but adding a service is not; I agree with the rest.” The correction is clear, so no further interview is needed. The agent rechecks the relevant constraint, records the clarification in a revised spec, preserves other valid content, and presents the draft for approval before continuing. Unrelated product-profile questions are not added to reach a quota; checking code and test evidence is the agent's job.

The old conversation does not need to run again. After confirmation, the new conversation uses current tools and consults historical evidence only when needed.

### Limits

This is an agent workflow, not a standalone session importer. It needs readable source records and a working destination conversation; an ID alone cannot recover inaccessible content. It does not bypass account quotas or clear already-loaded context. Invoke it in a fresh conversation and avoid copying the entire old log. A platform-independent workflow does not mean every agent has been tested or that data access is automatically available across applications; record reading, path verification, and execution depend on the current environment. Cross-model support does not guarantee identical interpretation quality.

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
