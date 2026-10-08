> 2026-10-08 主窗口修正并发推论与 DBOS 重量断言；现行可选沙箱/调度能力须按目标版本比较，不以本表将责任全部预定为自建。见 [局部复核](../主窗口复核-2026-10-08.md)。

# framework-06 候选依据 · PydanticAI（Core + Harness 及必要配套）

Status: RESEARCH/CANDIDATE（framework-06 候选依据），日期 2026-10-07（Asia/Shanghai）。
执行：Hermes 子代理。范围：官方文档／源码只读核对 + 既有原型证据（framework-04/05）逐条对照；**未安装依赖、未跑原型/产品测试、未连接模型或服务、未 SSH/部署/提交**。
说明：本文件只覆盖 PydanticAI 候选，不改写旧报告、不改任何共享正文。所有判断标注层次：

- **①框架核心原生**（`pydantic-ai-slim` / `pydantic-ai`）
- **②官方可选能力或部署层**（`pydantic-ai-harness` 或 durable engine/UI 适配）
- **③已有项目资产**（framework-04/05 原型里由项目自写、验证通过的外层件）
- **④必要新增适配**（接故渊必须新写，框架不提供）
- **⑤尚缺或未验**（框架无此能力，或只有文档声称、本项目未验）

**旧原型已验** = framework-04/05 保存的运行证据里实际跑过；**现行文档声称** = 2026-10-07 查阅的滚动官方文档描述，未在本项目固定版本上运行。

---

## 0. 结论摘要

1. **Core 能直接承担主运行内核**：`Agent`（typed agent loop）、`run`/`run_sync`/`run_stream_events`、工具循环、`deps_type`+`RunContext` 依赖注入、`message_history` 会话续接、`CancellationToken`/`ctx.cancel()` 取消与 `RunCancelled` 结束语义，均属①；旧 `RunSegment` 模型/工具循环、wait/idle 语义在能力上可退出（取消失败只覆盖“配合取消”的工具，见 §3）。
2. **Harness 0.36.0 提供四件关键官方件**（均②，且 framework-04/05 已实跑）：`SubAgents` 委派、`FileSystem(root_dir=...)` 路径限制文件工具、`ToolOutputLimits`+`Spill`+`read_tool_result` 大结果落盘/读回、`StepPersistence` 走点快照+工具效果账本（SQLite 等）。
3. **会话/长期状态**：Core 用 `message_history=` + `ModelMessagesTypeAdapter`（JSON round-trip）+ `conversation_id`/`run_id` 标识（①）；没有“thread/会话对象”；Harness `StepPersistence` 用 `conversation_id` 分组并提供可续/可 fork 快照与 tool-effect ledger（②）。旧原型**只验了“外层记录+作品续做”**，未验序列化 `message_history` 恢复（§3 场景3）。
4. **持久执行不是 Core 自带**：是 Core 的 durable execution 能力，必须**接一个 engine**，且**每 agent 恰好一个 engine**。官方支持 8 个：Temporal/DBOS/Prefect（与厂商共同维护）、Restate/AWS Lambda，及 Kitaru/Airflow/Absurd；另有 backend builder。**DBOS 是库内进程内运行**（只需一个系统数据库），Temporal/Prefect 需要各自 server/服务。旧原型**未接任何 durable engine**（⑤）。
5. **定时与条件调度：Core 与 Harness 都没有原生能力（⑤）**。`pydantic-ai-harness` 的 “Scheduled Agent Execution (cron / time-based triggers)” 仍是**未合并的 issue #111**；core 亦有对应 open issue。这是本候选对“真实时刻/条件唤醒”的**核心缺口**，最小配套是外部调度器 + 项目自写登记/去重/补检（④）。
6. **HITL/审批与受控扩展点齐备**：`requires_approval=True`、`ApprovalRequired`、`DeferredToolRequests`/`DeferredToolResults`、`HandleDeferredToolCalls`、`CallDeferred`（外部执行）为①；能力生命周期 hook（`before_run/after_run/wrap_run`、`before/after/wrap_tool_execute`、`before_model_request`、`handle_deferred_tool_calls`、`on_run_error`）为①。framework-05 已实跑 `AbstractCapability.after_tool_execute` 与 `ProcessHistory`（①+④ 适配）——**这是递入 current 与签发 receipt 的候选受控点**，但“receipt”本身不是框架概念，须新写（④）。
7. **前端边界**：`UIAdapter`（AG-UI、Vercel AI 两种协议）负责 run input ↔ `Agent.run_stream_events()` ↔ SSE 编码；可在“agent 不在请求内运行”时用 `UIEventStream` 单独编码（①/②）。适配器端点**不是鉴权边界**，须放进自有鉴权路由（④）。
8. **最小组合**：跑得起来只需 `pydantic-ai-slim[<provider>]`（①）；用到的 Harness 能力再单加 `pydantic-ai-harness`（②）；只有需要“**同一 run 工具中途崩溃续跑**”才再选**一个** durable engine（②，DBOS 为候选，未核实完整组合最轻）；定时/外部检测在任何情况下都是**外部或自写**（⑤→④）。
9. **旧原型已验 vs 现行文档声称（关键差异）**：`pydantic_ai.workspaces`/`LocalWorkspace`/`ExecutionEnvironment`、`Memory`、`ConversationSearch`、`Skills`、Guardrails、CodeMode、各 sandbox、deferred tools、durable engine 等均为**现行滚动文档声称**，**未在固定 Core 2.51.0 / Harness 0.36.0 上验证**；framework-04 已记录“Core 2.51.0 中 `pydantic_ai.workspaces` 导入不可用”，故浮动文档的 workspace 术语与固定版本不可混用。
10. **与 ADR-002 一致，无冲突**：本候选“可承担主运行内核 + 适用调度/恢复交框架/配套”，与 ADR-002“故渊不必自建 wake loop、运行宿主可交框架”一致；ADR-002 §“原生能力层次核对”对 PydanticAI durable execution 的说法与本次现行文档核对相符（属 engine 层，不是 Core/Harness 工具循环自带）。

---

## 1. 版本与来源表

| 包/文件 | 版本 / tag / commit | 查阅日期 | 来源链接 |
|---|---|---|---|
| `pydantic-ai-slim`（Core） | **2.51.0**（旧原型基线） | 2026-10-07 | 原型锁文件 `D:/codex/prototypes/framework-04/pydantic-ai/requirements.in`、`requirements.lock`、`install.log` |
| `pydantic-ai-slim` v2.51.0 发布日期 | 2026-09-25（GitHub release） | 2026-10-07 | https://github.com/pydantic/pydantic-ai/releases |
| `pydantic-ai-harness` | **0.36.0**（旧原型基线；`requirements.in` 精确锁定） | 2026-10-07 | 原型 `requirements.in`/`requirements.lock`/`install.log`；滚动文档 https://pydantic.dev/docs/ai/harness/ |
| `pydantic-ai-harness` 版本策略 | 0.x：minor 可含 breaking change | 2026-10-07 | https://pydantic.dev/docs/ai/harness/ ；仓库 README |
| 传递依赖（旧原型） | `openai==3.24.0`、`pydantic==2.13.5`、`pydantic-graph==2.51.0`、`httpx==0.28.1`、`anyio==4.15.1` | 2026-10-07 | `install.log`（30 个锁定 distribution） |
| Core：Agent / run | `pydantic_ai.Agent`、`run`/`run_sync`/`run_stream_events`/`iter` | 2026-10-07 | https://pydantic.dev/docs/ai/api/pydantic-ai/agent/ |
| Core：依赖注入 | `deps_type=`、`RunContext[...]`、`ctx.deps` | 2026-10-07 | https://pydantic.dev/docs/ai/core-concepts/dependencies/ |
| Core：消息历史 | `message_history=`、`all_messages()`/`new_messages()`、`ModelMessagesTypeAdapter`、`conversation_id`/`run_id` | 2026-10-07 | https://pydantic.dev/docs/ai/core-concepts/message-history/ |
| Core：取消/结束 | `CancellationToken`、`RunContext.cancel()`、`AgentRun.cancel()`、`RunCancelled.all_messages()` | 2026-10-07 | https://github.com/pydantic/pydantic-ai/blob/main/pydantic_ai_slim/pydantic_ai/.agents/skills/building-pydantic-ai-agents/references/INPUT-AND-HISTORY.md |
| Core：Handler/审批 | `HandleDeferredToolCalls`、`DeferredToolRequests`/`DeferredToolResults`、`CallDeferred`、`requires_approval=True` | 2026-10-07 | https://pydantic.dev/docs/ai/tools-toolsets/deferred-tools/ ；https://pydantic.dev/docs/ai/capabilities/handle-deferred-tool-calls/ |
| Core：能力/hook | `AbstractCapability`、`Hooks`、`ProcessHistory`、生命周期 hook 列表 | 2026-10-07 | https://pydantic.dev/docs/ai/core-concepts/hooks/ ；https://pydantic.dev/docs/ai/capabilities/custom |
| Core：durable execution | 8 engine + backend builder；每 agent 一个 engine | 2026-10-07 | https://pydantic.dev/docs/ai/capabilities/durable_execution/overview/ |
| Durable：Temporal | 需 Temporal Server（本地或独立服务） | 2026-10-07 | https://pydantic.dev/docs/ai/capabilities/durable_execution/temporal/ |
| Durable：DBOS | “fully in-process as a library”，需系统数据库；含 Queues/Cron Jobs | 2026-10-07 | https://pydantic.dev/docs/ai/capabilities/durable_execution/dbos/ |
| Durable：backend builder | `CallableOperationBackend`/`RegisteredOperationBackend`/`DurabilityEngineSpec` | 2026-10-07 | https://pydantic.dev/docs/ai/capabilities/durable_execution/backends |
| Harness：概览/能力清单 | FileSystem/Shell/Skills/RepoContext/Memory/SubAgents/ToolOutputLimits/StepPersistence/ConversationSearch/… | 2026-10-07 | https://pydantic.dev/docs/ai/harness/ |
| Harness：SubAgents | `SubAgents`/`SubAgent`、`delegate_task(agent_name, task)`、per-delegate `max_calls/timeout_seconds/usage_limits` | 2026-10-07 | https://pydantic.dev/docs/ai/harness/subagents/ |
| Harness：FileSystem | `root_dir`、`allowed_patterns`/`denied_patterns`/`read_only_patterns`、`tools=[...]`、`content_hashes`/`expected_hash` | 2026-10-07 | https://pydantic.dev/docs/ai/harness/filesystem/ |
| Harness：ToolOutputLimits | `Band`/`Spill`/`Summarize`/`Truncate`、`LocalFileStore`/`OverflowStore`、`read_tool_result(handle, offset, limit, from_end, pattern)` | 2026-10-07 | https://pydantic.dev/docs/ai/harness/tool-output-limits/ |
| Harness：StepPersistence | `StepPersistence`、`SqliteStepStore`、`ContinuableSnapshot`、tool-effect ledger | 2026-10-07 | https://pydantic.dev/docs/ai/harness/step-persistence/ |
| Harness：Memory | `Memory(FileStore(...))`、写/读/删/搜工具，按 namespace 而非 `conversation_id` | 2026-10-07 | https://pydantic.dev/docs/ai/harness/memory/ |
| 调度缺口 | Harness issue #111 “Scheduled Agent Execution (cron / time-based triggers)”（未合并） | 2026-10-07 | https://github.com/pydantic/pydantic-ai-harness/issues/111 |
| UI 流式 | `UIAdapter`（AG-UI `AGUIAdapter` / Vercel `VercelAIAdapter`）、SSE 编码、`on_complete`/`on_cancel` | 2026-10-07 | https://pydantic.dev/docs/ai/integrations/ui/ |
| 旧原型证据（framework-04） | `prototype.py`、`run_validation.py`、`evidence/validation.json`、`requirements.lock` | 2026-10-07 | `D:/codex/prototypes/framework-04/pydantic-ai/` |
| 旧原型证据（framework-05） | `runner/pai_run.py`、`runner/pai_history.py`、`evidence/results-pai.json`、`evidence/run-meta-pc*.json`、`evidence/stub-requests-pc*.jsonl` | 2026-10-07 | `D:/codex/prototypes/framework-05/pydantic-ai/` |

**来源边界提示**：截至查阅日，我能从公开 release 列表看到的 Harness 版本号最高为 0.31.0；**0.36.0 仅由旧原型锁文件确认**。下文凡属“现行文档声称”的 Harness 能力，版本锚点是最新滚动 0.x 文档，**不保证与 0.36.0 逐一对应**（Harness 0.x 允许 minor breaking change）。

---

## 2. §4 七格对照表

### 格 1 · 主会话与运行（谁承载模型/工具循环、状态注入、结果引用、排队、取消与结束）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ① 原生 | `pydantic_ai.Agent` + `run`/`run_sync`/`run_stream_events`/`iter` 的 typed tool loop；register via `@agent.tool_plain`/`@agent.tool` | 旧原型已验：framework-04 `prototype.py build_agent`、`r1.json`（search/read/write 5 次本地模型请求）；framework-05 `pai_run.py`（4 个未用 tool_plain + C1 7/7） |
| ① 原生 | 状态注入＝`ProcessHistory` 处理器或直接 `before_model_request` hook（可改写每请求 history） | 旧原型已验：framework-05 `pai_history.py Fw05HistoryProcessor` 经 `ProcessHistory` 注入单份状态块、按轮 slim（C1/C3 pass） |
| ① 原生 | 消息历史/结果引用：`message_history=`、`all_messages()`/`new_messages()`、`ToolReturnPart.outcome`、`ToolReturnPart.metadata` | 旧原型已验：framework-05 `pai_run.py` 循环里 `history = result.all_messages()`；`run-meta-pc*.json`；`results-pai.json` C2.outcome |
| ① 原生 | 取消/结束：`CancellationToken`/`agent_run.cancel()`/`ctx.cancel()`，`RunCancelled`/`RunCancelled.all_messages()` | 旧原型已验：framework-04 `prototype.py build_cancel_agent`+`test_agent_runtime.py`（取消传播到固定 async tool，`CancelledError`）；现行文档补齐 `RunCancelled.all_messages()` 快照语义（未在本项目运行） |
| ③ 资产 / ④ 适配 | 旧 `RunSegment`/`wait/idle`/串行排队在能力上可退出；排队与“一唤醒一会话”的边界由外层持有 | ADR-002 表格；framework-05 `pai_run.py` 每 run 显式传 `message_history`（无隐式 session） |

### 格 2 · 本人任务与子代理（本人独立工作 vs 代做；长工作/等待期间继续主对话；子代理运行单元；产物接收）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ② 官方可选 | `pydantic_ai_harness.SubAgents`/`SubAgent`，暴露单一 `delegate_task(agent_name, task)`；子代理**独立 run、独立 message history**；per-delegate `max_calls`/`timeout_seconds`/`usage_limits`；取消/usage-limit/控制流信号会穿透 containment | 旧原型已验：framework-04 `run_validation.py`（`SubAgents([SubAgent(child,name='reviewer',max_calls=1)])`，parent→child→parent 4 次桩请求，`validation.json D1`） |
| ① 原生 | 把长任务甩出当前 run：`CallDeferred` → `DeferredToolRequests`（外部执行），或 `HandleDeferredToolCalls` 内联解析 | 现行文档声称（未在本项目运行） |
| ④ 适配 | “本人独立任务”与“子代理代做”的区分、父子 actor/授权、异步后台 worker、结果回流与主对话继续，均为业务层新写；`delegate_task` 默认是**父 run 内的一次工具调用**（同进程、阻塞该轮），要在长等待中继续主对话需另用 deferred/external 路径或独立 worker | framework-04 复核已指出“产品级异步子任务生命周期、重启回执、OS 身份隔离仍须外层实现” |
| ③ 资产 | 产物接收：外层 `ArtifactStore`（版本/哈希/来源）、任务 manifest | 旧原型已验：framework-04 `prototype.py ArtifactStore`（story.v1/v2、review.v1/v2）；framework-05 `harness.ArtifactStore` |
| ⑤ 未验 | 子代理权限范围强制（framework-04 D1 中 child 工具实际可读 source-a/source-b，manifest 写 `read:fixture-a` 但未单独拒绝 source-b） | framework-04 复核 §3 |

### 格 3 · 真实定时与条件（一次性时刻/周期/外部变化；注册/取消/去重/重启补检；谁决定重新检查/执行/通知）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ⑤ 尚缺 | **Core 与 Harness 均无原生定时/调度能力**；无 `Scheduling` capability | Harness 能力清单无调度项（https://pydantic.dev/docs/ai/harness/）；Harness issue #111、core issue #9163（scheduling）均为**未合并 feature request** |
| ② 官方可选（engine 层） | 若已选用 durable engine，**DBOS** 自带 Cron Jobs / Queues（数据库后端的定时与队列），但那是 DBOS 引擎能力，**不是 PydanticAI Core/Harness 的调度** | https://pydantic.dev/docs/ai/capabilities/durable_execution/dbos/ |
| ④ 适配 | 一次性/周期时刻登记、取消、去重、重启补检：需外部调度（系统 cron、容器调度或所选 engine 的 cron）+ 项目自写登记表与补检逻辑 | ADR-002 §“旧机制与新方向取舍”已定性“调度到点不自动知道网站变化，仍需数据接入/条件判断” |
| ④ 适配 | 外部变化检测：接入数据源、条件判断、按当前授权决定行动/通知；“命中不等于授权执行” | 00c §6/§11；Project/权限边界不属框架 |
| ① 原生（边缘） | 运行内可调 `WebFetch`/`WebSearch` 等 Core 能力读一次内容，但**不做变更检测/去重/阈值** | 现行文档声称（README 示例 `from pydantic_ai.capabilities import WebFetch, WebSearch`），未在本项目运行 |

**结论**：真实时刻/条件登记与外部变化**完全不落在这套框架的 Core/Harness 里**（⑤），必须在外部调度 + 项目适配落地（④）。这正是 framework-02/00d 反复强调“承载方未定”的格子。

### 格 4 · 持久恢复（会话重开、同一 run 崩溃恢复、从作品/任务记录续做；重复哪些动作如何核对）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ① 原生 | 会话重开＝自己存 `message_history` 字节（`ModelMessagesTypeAdapter` round-trip，含 `metadata`）、`conversation_id` 归组、`new_messages()` 增量追加 | 现行文档声称；旧原型**只验**外层记录注入 + 新 run（framework-05 C5A/C5B） |
| ② 官方可选 | `StepPersistence`（`SqliteStepStore` 等）：每个 settled step 存可续/fork 快照 + append-only step event + **tool-effect ledger**（`started`/`completed`/`failed` per `(run_id, tool_call_id)`） | 旧原型已验（部分）：framework-04 `validation.json`（2 run / 7 snapshots）；**但**同 run 中途恢复未验，`latest_snapshot`/`continue_run` 只回 `complete` 快照 |
| ② 官方可选（engine 层） | 同一 run **工具中途**崩溃续跑＝durable execution（接一个 engine）；官档明确“mid-step crash 才用 durable execution，StepPersistence 只到 settled 边界” | https://pydantic.dev/docs/ai/harness/step-persistence/ ；durable_execution overview。旧原型未接任何 engine（⑤） |
| ③ 资产 | 从作品/任务记录续做：外层 manifest + artifact 版本 + 显式新 run | 旧原型已验：framework-04 L1/L2（新进程读 artifact v2 续做/确认，`migrate-fresh` 只读确认、未写下一阶段）；framework-05 C5B（v2 由实际读回的 v1 派生） |
| ⑤ 未验 | 真实外部副作用的 exactly-once / 对账；`StepPersistence` 的 tool-effect ledger 在**真实** Agent/tool 崩溃路径中的判定 | framework-04 复核：effect 崩溃是独立 Python/JSON 模拟，未经过候选 Agent/tool 恢复路径 |

### 格 5 · 认知 / authority 接入（受控扩展点递入 current、结算、主体认领与 receipt）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ① 原生 | 递入 current：`before_model_request`/`ProcessHistory`（改写请求视图）、`RunContext`(=`ctx.deps`/`ctx.messages`) | 旧原型已验：framework-05 `pai_history.py`（外层记录→状态块，C1/C5A/C5B pass） |
| ① 原生 | 受控点：能力生命周期 hook（`before_run/after_run/wrap_run`、`before/after/wrap_tool_execute`、`handle_deferred_tool_calls`、`on_run_error`）；`after_tool_execute` 可观察/改工具返回 | 旧原型已验：framework-05 `pai_run.py Fw05Capture.after_tool_execute`（记录框架交回的原始工具返回+哈希） |
| ① 原生 | 主体认领前的“批准闸门”：`requires_approval=True`、`ApprovalRequired`、`DeferredToolRequests`/`DeferredToolResults`、`ToolApproved`/`ToolDenied`+`override_args`、`RunContext.tool_call_approved`、`ToolDenied.message` | 现行文档声称（未在本项目运行） |
| ④ 适配 | **receipt** 不是框架概念：签发/持久审计凭据、域内 actor/capability/scope 校验、跨进程重放/失效，须在 hook/工具/适配里自写；框架只提供“谁在何时调用/被批准”的钩子与 metadata | 00c §9.1；框架无对应类型 |
| ⑤ 未验 | 模型正文不能自报“已授权/已完成”的强制：框架不校验业务真值，须由 receipt + 执行端结果承担（④） | ADR-002 §术语与责任 |

### 格 6 · 对话 / 前端边界（主动结果、无文本结束、SSE/断线重连归谁）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ①/② 原生 | `UIAdapter`（`AGUIAdapter`/`VercelAIAdapter`）：run input ↔ `Agent.run_stream_events()` ↔ SSE；`dispatch_request`/`run_stream`/`encode_stream`/`streaming_response`；`on_complete`/`on_cancel`（取消回 `RunCancelled`） | 现行文档声称（未在本项目运行；framework-05 桩支持 SSE 但产品流式未跑） |
| ② 官方可选 | agent 不在请求内的场景：用 `UIEventStream` 单独编码；transport 必须携带整段 `Agent.run_stream_events()`（含 `AgentRunResultEvent`）才能收尾 | 现行文档声称 |
| ④ 适配 | 主动结果/无文本结束的递回：适配器端点**不是鉴权边界**，须放进自有鉴权路由；断线重连、重放、thread/run 关联由外层 transport 承担 | https://pydantic.dev/docs/ai/integrations/ui/ |
| ③ 资产 | Tidal/React 前端、既有 API 幂等受理与独立 SSE：framework-04/05 未接产品前端 | 交接 §4 表（未验） |

### 格 7 · 运行组合与成本（仅装核心是否够；Harness/部署层/持久引擎/外部调度何时必要；新增维护/部署负担）

| 判定层次 | 具体包/模块 | 依据 |
|---|---|---|
| ① 核心 | 最小可用＝`pydantic-ai-slim[<provider>]`（或整包 `pydantic-ai`）+ 模型 provider extra；旧原型即 `pydantic-ai-slim[openai]==2.51.0` | 旧原型 `requirements.in`；官档 “pydantic-ai-harness 会一并装 slim” |
| ② 可选 | Harness 只在用其能力时装（`pydantic-ai-harness`，含 `[code-mode]/[cli]` 等 extra）；本候选关键依赖：`SubAgents`/`FileSystem`/`ToolOutputLimits`/`StepPersistence` | 旧原型 `pydantic-ai-harness==0.36.0`；官档 |
| ② 部署层（按需） | durable engine 只有需要“同 run 工具中途崩溃续跑”才选**一个**：DBOS（库内进程内，需 DB）／Temporal（需 Temporal Server）／Prefect（需其服务）等 | durable_execution overview/temporal/dbos |
| ④ 外部/自写 | 调度与外部检测：**框架不提供**，须外部调度器 + 项目适配（格 3） | Harness issue #111 |
| ③ 资产 | 需要保留的外层件：任务 manifest、artifact store+版本哈希、状态块构建、统一判据 | framework-04/05 原型（可整块沿用） |

---

## 3. §5 三场景（原生覆盖 / 缺口 / 失败或未知 / 证据）

### 场景 1 · 研究期间继续交谈（主体长研究 + 只读子代理，等待时用户发消息）

时序：

1. 用户消息 → 主体主 `Agent.run`（①）：工具循环做研究；外层用 `message_history` 维持对话。
2. 主体在该 run 内委派一次只读子代理：`delegate_task(agent_name, task)`（② `SubAgents`）→ 子代理**独立 run、独立 history**，只拿到其工具集。framework-04 实测：child 只挂 `read_review_source`，回传后 parent 续写。
3. 子代理产物由**外层 ArtifactStore** 接收并做版本/来源（③）。
4. 等待期间用户再发消息：新的一次 `Agent.run(message_history=...)` 即可在同一会话继续（①）；但**同一父 run 在等待 `delegate_task` 结果时**，其它异步 run 可继续；持久后台任务/权限与资源边界另定，不能推导必须独立 worker。

- **原生覆盖**：Agent loop、委派、独立子代理 history、会话续接、取消（①/②）。
- **缺口**：父/子身份与授权区分、异步后台 child worker、跨越等待期的消息排队、结果主动回流——框架不提供（④）；`delegate_task` 默认同进程阻塞父轮。
- **失败/未知**：子代理授权是否被真实限制**未验**（framework-04 D1 中 child 实际可读 source-b，未按任务范围拒绝）；符合同 scope 的 OS 身份隔离未验。
- **证据**：framework-04 `run_validation.py`、`validation.json D1`；framework-05 `pai_run.py`、`results-pai.json C1/C5A`。

### 场景 2 · 真实时刻与回复变化（登记“明日下午再看某论坛主题”，无新回复则沉默）

时序：

1. 主体登记一个未来时刻/条件。**框架无定时能力**（⑤）→ 须外部调度器写入到期项。
2. 到点由外部调度触发**一次新的 `Agent.run`**（①）；抓取论坛用什么工具由项目接入（Core 的 `WebFetch` 仅是单次读取，无变更检测）。
3. run 内做条件判断（有无新回复）：模型/工具逻辑（①＋业务④）；**无新回复时主动结束且不产出用户可见文本**。
4. 有新回复时“先重新检查、再按当前授权行动/通知”：行动合法性走 deferred/审批闸门（①）与业务 receipt（④）；通知走 UI 适配/外层（④）。

- **原生覆盖**：仅“运行一次 run、读一次内容、审批闸门、SSE 递回”这一层。
- **缺口（核心）**：定时/条件调度在 Core 与 Harness **都不存在**（Harness issue #111 未合并）；登记/取消/去重/重启后到期项补检、变更检测、阈值，全部要外部调度＋项目自写（④）。
- **失败/未知**：没有调度就无法开工；DBOS 的 Cron 只在选了 DBOS 时可用（是 engine 能力，非框架调度）；不偷加保底心跳（框架本也没有）。
- **证据**：https://github.com/pydantic/pydantic-ai-harness/issues/111 ；https://pydantic.dev/docs/ai/harness/ ；ADR-002 §“原生能力层次核对”。

### 场景 3 · 停机后继续创作（工具已写作品 v1，进程停止，完成回执未到；重启续做）

时序：

1. 工具写入 v1（外层 ArtifactStore，③，已验）。
2. 进程在**工具结束到回执之间**停止。
3. 重启后判“恢复的是 session/run 还是任务成果”：
   - ① 重开会话：外层序列化 `message_history`（`ModelMessagesTypeAdapter`）+ `conversation_id` 再跑新 run（**旧原型未验序列化恢复**）。
   - ② 走点恢复：`StepPersistence` 的 `ContinuableSnapshot`（只到 settled 边界）与 **tool-effect ledger** 判断该 `tool_call_id` 是 `completed` 还是 `unknown`；`latest_snapshot`/`continue_run` 默认只回 `complete` 快照。
   - ②/① 同 run 工具中途续跑：需 durable engine；**旧原型未接任何 engine**（⑤）。
   - ③ 从作品续做：新进程凭 manifest/artifact v2 继续（framework-04 L2 只读确认、**未写下一阶段**；framework-05 C5B 由 v1 原文派生 v2，已验）。
4. “不能靠重放补编成功”：核对依据是 tool-effect ledger + artifact 版本/哈希；框架的 durable/step 记录**不证明业务完成**（④ 业务完成验真）。

- **原生覆盖**：会话序列化边界（①）、settled 走点快照与效果账本（②）。
- **缺口**：同 run 工具中途崩溃恢复要自选并接一个 engine（②，未验）；跨进程恢复 `spill` handle、历史轮映射、错误停点未验（framework-05 §3）；业务“真实完成”须执行端结果 + receipt（④）。
- **失败/未知**：framework-04 的 effect 崩溃是**独立 Python/JSON 模拟**，不经候选 Agent/tool 恢复路径，不能与真实崩溃等同；L2 未真正写下一阶段。
- **证据**：`validation.json`（effect_recovery、L1/L2）；`evidence/long-first.stdout.txt`、`long-second.stdout.txt`、`migrate-fresh.stdout.txt`；framework-05 `results-pai.json C5B`；StepPersistence 官档。

---

## 4. 最小组合与真实剩余工作

**最小组合（按用途分级）**

- 跑主会话/工具循环/依赖注入/会话续接/取消：只需 **①`pydantic-ai-slim[<provider>]`**。
- 用官方文件工具、大结果落盘读回、委派子代理、走点持久化：加 **②`pydantic-ai-harness`**（`FileSystem`、`ToolOutputLimits`、`SubAgents`、`StepPersistence`）。
- 需要“**同一 run 工具中途崩溃续跑**”：再加**恰好一个** durable engine（②，`pydantic-ai-slim[dbos]` 最轻——库内进程内 + 系统 DB；`[temporal]`/Prefect 需各自 server）。
- 定时与外部变化检测：**不在框架内**（⑤），必须外部调度 + 项目适配（④）。

**要写什么适配（④）**

1. 任务/子任务/唤醒的业务层：submit/status/cancel/result、幂等键、actor/parent、每任务授权、来源与运行引用、审阅事件；旧 `TaskStore` 不能直接当产品 schema。
2. 定时/条件登记与外部变化检测：登记/取消/去重/重启补检、数据接入、条件判断；与实际执行/通知分开。
3. authority/receipt：域内决定者、actor/capability/scope 校验、持久审计 receipt、跨进程校验/失效；“模型正文不得自报已授权/完成”。
4. 前端边界：把 `UIAdapter`/`UIEventStream` 放进自有鉴权路由；主动结果/无文本结束的递回、断线重连、thread/run 关联。
5. 真实工具与隔离：Shell/浏览器/网络工具须另选并由 OS/容器限权（`FileSystem.root_dir` **只限文件工具，不是隔离**）；模型出口经 Serein（未验）。

**产物由谁接收**：主体/子代理产物进**外层 ArtifactStore**（版本/哈希/来源，③可沿用）；运行态快照由 `StepPersistence` store（②）持有；二者的归属与真源边界由业务层决定（④）。`StepPersistence` 的 run/checkpoint ID 与 message history **不可作为跨执行器可移植成果契约**，可移植的是外层 task/artifact 契约（framework-04 复核）。

---

## 5. 部署与资源约束（只写组成/常驻进程/外部依赖/配置边界，不给数字）

- **组成**：一个 Python worker 进程承载 Agent run（①）；可选启用 Harness 能力（②）会额外产生磁盘文件（spill 落盘于 `.pydantic-ai-harness/` 或自定 root；StepPersistence 用 SQLite/文件/MongoDB）。
- **常驻进程**：
  - 只装 Core：**无额外常驻守护进程**，进程即一次 run。
  - 接 DBOS：**库内进程内**，无独立 server，但依赖一个**系统数据库**（并自带 Queues/Cron 为可选）。
  - 接 Temporal：**需要 Temporal Server 常驻**（本地或独立服务），worker 连其 task queue。
  - 接 Prefect：需其 server/部署层。
  - 调度/外部检测：**框架外**，须另设调度器（系统 cron、容器调度或所选 engine 的 cron）。
- **外部依赖**：模型 provider（本项目经 Serein）；durable engine 的服务/数据库（按所选 engine）；harness spill store 的磁盘；UI 适配依赖 Starlette/FastAPI 生态（若走其 `dispatch_request`）。
- **配置边界（举例，均官方文档声称）**：`FileSystem(root_dir, allowed_patterns, denied_patterns, read_only_patterns, tools, content_hashes)`——`root_dir` **不是 OS 沙箱**，`Shell` 不受其约束；`ToolOutputLimits(bands, per_tool, store, serializer)`，默认 spill 阈值 10,000 字符、默认 store `LocalFileStore`（0700、拒绝越界 handle）；durable 维度“**每 agent 恰好一个 engine**，装第二个即 `UserError`”。
- **不写**：内存/工期/节省比例/VPS 并发数字。旧原型只给出**本机 Windows 定性观察**（framework-05 §4.4，样本 1–3 点），**不能外推** Ubuntu VPS 峰值或并发（VPS 容量文档明确）。

---

## 6. 未验项清单 + 一项最小离线验证建议

**未验项（旧原型未取得运行证据或现行文档声称未在本项目跑过）**

1. `pydantic_ai.workspaces`/`LocalWorkspace`/`ExecutionEnvironment`：Core 2.51.0 中 `pydantic_ai.workspaces` **导入不可用**（framework-04 记录）；浮动文档的 workspace 术语**不套作固定版本已验证能力**。
2. durable execution：**未接任何 engine**；同 run 工具中途崩溃续跑未验；真实副作用对账未验。
3. `message_history` 的**序列化往返恢复**未验（旧原型只验外层记录注入 + 新 run）。
4. HITL/审批（`requires_approval`/`DeferredToolRequests`/`CallDeferred`）、`Memory`、`ConversationSearch`、Guardrails、CodeMode、各 sandbox：**现行文档声称、未在本项目运行**。
5. 流式/SSE 产品边界：framework-05 桩支持 SSE，但产品流式/断线重连未跑。
6. `SubAgents` 授权范围强制（child 越权读）、异步后台 child、OS/容器隔离未验。
7. 真实模型/Serein/网搜/浏览器、真实工具与外部副作用、跨执行器交接均未验。

**一项最小离线验证建议（静态材料无法回答的关键点：同一 run 工具中途崩溃的恢复与“已做动作”判定）**

- **建议**：在离线、只连本机脚本化模型桩、不接产品/Serein 的前提下，构造一个最小 agent：一个 async tool 先向外层文件写入一个带内容哈希的“效果记录”，再在工具**尚未返回**时杀死进程；随后在同一 `SqliteStepStore`/自定 store 上，用 `StepPersistence` 的 `ContinuableSnapshot` 与 tool-effect ledger 判断该 `tool_call_id` 的 `started`/`completed` 状态，并让新进程据此**只读确认/续做**（不重复写入）。
- **通过条件**：（a）重启进程能从 store 读到该 tool_call 的记录，且能区分“已完成/未知”；（b）业务侧据此判定后**不重复**写入效果记录（文件哈希不新增第二版）；（c）若改用 durable engine，需对比其在这条路径上是否比 StepPersistence 更能判定中途状态。静态材料无法给出该通过结论，**故仅建议，未运行**。

---

## 附：与任务卡 / ADR-002 的冲突检查

- 未发现与任务卡 §4/§5 或 ADR-002 的冲突。ADR-002 §“原生能力层次核对”对 PydanticAI durable execution“属 engine 层、不能由 Core/Harness 有工具循环直接推定常驻定时器”的说法，与本次核对一致。
- 一处**证据边界提示（非冲突）**：Harness `0.36.0` 仅由旧原型锁文件确认，公开 release 列表未列到该 tag；本报告把“现行滚动 0.x 文档声称的能力”与“旧原型固定版本已验能力”分列，未把新文档能力倒算为旧原型已验。
